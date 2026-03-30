# Near MPC Chain Signatures — Analysis for Midnight OS

## What Near Built

A **production threshold signature network** where 8 independent MPC nodes sign transactions on any blockchain (Bitcoin, Ethereum, Solana, etc.) on behalf of a NEAR account. A single NEAR identity controls deterministic addresses on every supported chain.

This is essentially what the Midnight OS MVE envisions — one identity, multiple chains, no separate key management per chain.

**Production stats:**
- 8 independent MPC nodes
- ~$0.001 per signature (7 TGas on NEAR)
- Supports ECDSA (secp256k1), EdDSA (Ed25519), BLS (BLS12-381)
- Continuous background precomputation (up to 1M Beaver triples stored per node)

---

## Architecture Overview

```
User calls v1.signer smart contract on NEAR
    ↓
Contract stores the sign request
    ↓
MPC nodes detect request via NEAR indexers
    ↓
Nodes exchange partial signatures over TLS 1.3 P2P mesh
    ↓
Combined signature submitted back to contract
    ↓
User receives valid signature, broadcasts to target chain
```

**Key architectural decision:** On-chain coordination, off-chain computation. The NEAR smart contract is just a mailbox — it receives requests, manages participants, and delivers results. All cryptographic operations happen off-chain between the MPC nodes.

---

## Cryptographic Stack

### Signature Providers

| Provider | Curve | Protocol | Use Case |
|---|---|---|---|
| ECDSA (OT-based) | secp256k1 | Beaver triples + OT | Bitcoin, Ethereum, EVM chains |
| Robust ECDSA | secp256k1 | 3-round presignature (no triples) | Byzantine fault-tolerant variant |
| EdDSA | Ed25519 | Standard FROST | Solana, Polkadot, Cosmos |
| CKD | BLS12-381 | ElGamal + threshold decryption | App-specific confidential key derivation |

### Key Libraries

```
frost-core 2.2.0          — Base threshold signature framework
frost-ed25519 2.2.0        — FROST on Ed25519
frost-secp256k1 2.2.0      — FROST on secp256k1
k256 0.13.4                — secp256k1 operations
curve25519-dalek 4.1.3     — Ed25519 operations
blstrs 0.7.1               — BLS12-381 signatures
reddsa (forked from Zcash)  — FROST-compatible EdDSA
sha2, sha3, keccak          — Hash functions
```

### No Paillier

This is a critical difference from GG20/CGGMP. Near's OT-based ECDSA uses **Oblivious Transfer** and **Beaver triples** instead of Paillier encryption. The operations are:
- Scalar multiplications on secp256k1
- SHA-256/SHA-3 hashing
- Polynomial evaluation (Lagrange interpolation)
- Correlated OT extension for triple generation

No 2048-bit RSA. The most expensive operations are elliptic curve scalar multiplications, not big-integer modular exponentiation.

---

## Key Generation (FROST DKG)

Standard FROST distributed key generation:

```
1. Each participant generates random polynomial: f_i(x) = a_0 + a_1·x + ... + a_{t-1}·x^{t-1}
2. Commits to coefficients: A = [a_0·G, a_1·G, ..., a_{t-1}·G]
3. Generates proof of knowledge: PoK = (R, μ) where R = k·G, μ = k + a_0·H(id, a_0·G, R)
4. Distributes secret shares: f_i(j) sent encrypted to each participant j
5. Each participant verifies: f_i(j)·G == Σ A_ik · j^k
6. Output: (private_share, public_key)
```

Key is never whole — each node holds only their share. Threshold t-of-n required for any operation.

### Resharing

When nodes join or leave the network:

```
1. Old participants reconstruct shares for new polynomial
2. New participants receive reshared key material
3. ZK proofs verify old key == new key
4. Invariant checks: new joiners must have zero secret, old members must have non-zero
5. On-chain voting confirms resharing complete
```

The public key stays the same. All derived addresses remain valid after resharing.

---

## Additive Key Derivation

How one NEAR account controls addresses on every chain:

```
tweak = SHA3("near-mpc-recovery v0.1.0 epsilon derivation:" || account_id || path)
derived_private_share = master_share + tweak
derived_public_key = master_public_key + tweak · G
```

**Examples:**
- `alice.near` + `"bitcoin,1"` → Bitcoin address `bc1q...xyz`
- `alice.near` + `"ethereum,1"` → Ethereum address `0x7a3...def`
- `alice.near` + `"ethereum,2"` → Different Ethereum address `0x99c...315`

**Properties:**
- Deterministic — same inputs always produce same address
- Non-interactive — any node can derive the public key locally (no MPC needed for derivation)
- Additive — the tweak is just added to the master key, so threshold signing works on derived keys without protocol changes
- Separated by purpose — different prefixes for signing vs CKD to prevent cross-domain confusion

---

## Beaver Triples and Presignatures

The key to fast signing — precompute expensive work in the background:

### Beaver Triples

```
Triple = (a, b, c) where c = a · b

Each node holds shares: (a_i, b_i, c_i)
Together they satisfy: Σ c_i = (Σ a_i) · (Σ b_i)

Generated via: Correlated OT Extension → MTA (Multiplicative-to-Additive) → Multiplication
Stored: Up to 1 million per node, continuously generated in background
```

### Presignatures

```
Uses 2 Beaver triples per presignature
Pre-generates partial signature components
When sign request arrives: 1 round to complete using presignature
Without presignature: multiple rounds needed
```

This is why Near can sign in milliseconds — the expensive work was done hours or days earlier.

---

## TEE Integration (Intel TDX)

Optional hardware security layer:

```
1. Participant approves Docker image hash on-chain
2. Operator starts Confidential VM (Intel TDX)
3. Dstack retrieves decryption key from hardware
4. Encrypted drive decrypted, Launcher starts
5. Launcher verifies MPC image hash, measures to RTMR3
6. MPC node starts, registers attestation on-chain
```

**Guarantees:**
- Backward secrecy — former nodes cannot access plaintext key shares
- Integrity — tampering detected via hardware attestation
- Confidentiality — key shares encrypted at rest with hardware-derived keys

---

## Foreign Chain Inspector

Pluggable verification of external chain state:

```rust
trait ForeignChainInspector {
    async fn extract(tx_id, finality, extractors) -> Vec<ExtractedValue>;
}
```

**Supported chains:**
- **Ethereum/EVM:** `eth_getBlockByNumber`, `eth_getTransactionReceipt`, finality levels (Finalized/Safe/Latest)
- **Bitcoin:** `getrawtransaction`, `getblock`, confirmation-based finality
- **Solana:** `getTransaction`, `getBlock`, finality levels (Processed/Confirmed/Finalized)

**Design:**
- Each extractor returns exactly one typed value (bounded, on-chain friendly)
- Deterministic RPC provider selection
- Timeouts and size limits enforced
- Result is signed by MPC nodes: `msg_hash = SHA-256(borsh(request || extracted_values))`

---

## Relevance to Midnight OS

### What maps directly

| Near component | Midnight equivalent | Effort to adapt |
|---|---|---|
| FROST DKG | Key generation for Midnight Passport | Direct reuse — FROST works on any curve |
| Additive key derivation | Midnight DID → chain-specific addresses | Swap SHA3 for Poseidon2, same additive structure |
| On-chain contract coordination | Starstream UTXO for sign requests | Rewrite as Starstream coordination script |
| Off-chain MPC signing | MPC in WASM container | Same architecture, different runtime |
| TEE integration | Midnight Passport server-side share holder | Same Intel TDX pattern |
| Foreign chain inspector | "Show your BTC balance" in Midnight dApp | Add Cardano support, same trait pattern |
| Multiple signature domains | One Midnight key → BTC + ETH + SOL addresses | Same domain registry concept |

### What changes

**1. Key derivation hash function**

Near uses SHA3. Nightstream uses Poseidon2 exclusively. For derivations that happen inside proofs, swap to Poseidon2. The additive structure stays the same:

```
Near:     tweak = SHA3(prefix || account || path)
Midnight: tweak = Poseidon2(prefix || account || path)
```

**2. Proving MPC operations**

Near has **no ZK proofs** of MPC correctness. Validators trust MPC nodes directly (threshold + TEE assumptions). Midnight could prove certain operations inside Nightstream:

| Operation | Prove in Nightstream? | Rationale |
|---|---|---|
| Key derivation (tweak computation) | Yes — cheap (Poseidon2 + field addition) | Proves the derived key is correct |
| Signature verification | Yes — medium cost (~10K-25K constraints) | Proves the MPC produced a valid signature |
| MPC protocol rounds (OT, triples) | No — too expensive, run off-chain | Same as Near, trust threshold + TEE |
| Passkey binding | Yes — one-time cost | Links passkey to Nightstream key |

**3. UTXO model vs account model**

Near uses NEAR accounts (account model) as the identity root. Midnight uses UTXOs (Starstream coroutines). The sign request flow changes:

```
Near:     User calls v1.signer.sign() → contract stores request → nodes index it
Midnight: User resumes sign-request UTXO → coordination script → MPC nodes observe
```

The coordination pattern is the same. The on-chain representation changes from contract state to UTXO state.

### The gap — what Near doesn't solve for Midnight

**1. Goldilocks-native signing**

Near signs with secp256k1 and Ed25519 — both 256-bit curves, non-native to Goldilocks. Near doesn't care because they don't prove signing in ZK. Midnight cares because verifying signatures inside Nightstream proofs costs ~10K-25K constraints per signature.

**2. Anonymous credentials**

Near's chain signatures have no privacy — the NEAR account, derivation path, and target chain address are all public. Midnight needs selective disclosure (anonymous credentials) which Near doesn't address.

**3. ZKTLS**

Near's foreign chain inspector trusts RPC providers directly. Midnight may want ZKTLS for trustless web data verification (did:web credentials).

---

## Key Takeaways for the MPC Proposal

**1. OT-based ECDSA is viable without Paillier.** Near proves this at production scale. This opens up ECDSA chain signatures for Midnight without the infeasible Paillier overhead. The OT/Beaver-triple approach uses only EC scalar muls and hashing — expensive in Goldilocks but not impossible.

**2. Additive key derivation is the right pattern.** One master key, deterministic derivation to chain-specific keys. Near's implementation is clean and the additive structure makes it compatible with threshold signing without protocol changes.

**3. Background precomputation (Beaver triples) is essential for UX.** Without it, signing requires multiple interactive rounds (seconds). With presignatures, signing is one round (milliseconds). Any Midnight MPC must have a similar precomputation pipeline.

**4. The on-chain/off-chain split is validated.** Smart contract as mailbox, MPC as off-chain service, indexers as the bridge. This works at scale. Midnight can follow the same pattern with Starstream UTXOs instead of NEAR contract state.

**5. TEE is a practical complement to threshold security.** Near uses TEE (Intel TDX) optionally for backward secrecy and hardware isolation. This aligns with the Midnight MVE's use of device Secure Enclaves for key shares.

**6. Multi-domain support from day one.** Near supports ECDSA + EdDSA + BLS from the same MPC network. Midnight should design for multiple signature domains from the start rather than committing to a single curve.

---

## Recommended Reading

- Near MPC source code: `/near-mpc/` in this workspace
- [NEAR Chain Signatures docs](https://docs.near.org/chain-abstraction/chain-signatures)
- [FROST paper (ePrint 2020/852)](https://eprint.iacr.org/2020/852)
- [Cait-Sith library](https://github.com/cronokirby/cait-sith) — threshold ECDSA used by 
