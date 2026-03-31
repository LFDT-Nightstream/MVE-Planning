## Secure onboarding design

> **[Full document](./docs/secure-onboarding-design.md)** | **[Implementation plan](./docs/plan.md)**

The spec covers the complete flow: seed generation inside a TEE, three-wallet derivation (shielded, public, fees), commit-reveal name registration, subsidized airdrop, social-account linking, and DeRec threshold recovery. Built on Midnight's cryptographic primitives (BLS12-381, Jubjub, Poseidon, Compact circuits).

### The onboarding flow

```
  User                    Mobile App               Midnight Chain
   |                         |                          |
   |   1. Scan QR code       |                          |
   |------------------------>|                          |
   |                         |                          |
   |   2. Seed generated     |                          |
   |      inside TEE         |                          |
   |      (256-bit BIP39)    |                          |
   |                         |                          |
   |   3. Three wallets      |                          |
   |      derived via        |                          |
   |      CIP-1852:          |                          |
   |      - Shielded (ZK)    |                          |
   |      - Night (public)   |                          |
   |      - Dust (fees)      |                          |
   |                         |                          |
   |   4. Name registered    |   commit-reveal circuit  |
   |      alice.midnight     |------------------------->|
   |                         |                          |
   |   5. NIGHT airdrop      |   subsidized by provider |
   |      (enables DUST      |<-------------------------|
   |       fee generation)   |                          |
   |                         |                          |
   |   6. Link social        |                          |
   |      accounts           |                          |
   |      (recovery factors) |                          |
   |                         |                          |
   |   7. Designate DeRec    |   helper pairing         |
   |      recovery helpers   |------------------------->|
   |                         |                          |
   |   DONE: Named account   |                          |
   |   with credentials,     |                          |
   |   multi-chain wallet,   |                          |
   |   and recovery setup    |                          |
   |   (~60s user time)      |                          |
```

### Architecture (6 layers + connection protocol)

```
                                                  dApp CONNECTION
+------------------------------------------------+ +----------+
|            LAYER 6: CHAIN ABSTRACTION          | |          |
|  Intent Router | MPC Signer | Cross-Chain Map  | |          |
+------------------------------------------------+ |          |
|            LAYER 5: NAMING                     | | Discovery|
|  Registry    | Resolver     | Wildcard/CCIP    | | (events) |
|  (Compact)   | addr/shield  | offchain subdns  | |          |
+------------------------------------------------+ | Session  |
|            LAYER 4: IDENTITY                   | | (CAIP-25)|
|  Attestation Trees | SBT Anchor | zkKYC Issuer| |          |
|  persistentHash    | non-xfer   | Groth16+FHE | | Transport|
|  nullifiers        | tokens     | off-chain   | | (inject/ |
+------------------------------------------------+ |  WC/deep)|
|            LAYER 3: ACCOUNT MODEL              | |          |
|  alice.midnight                                | | API      |
|  Full-Access Keys (TEE) | Function-Call Keys   | | (MN RPC) |
+------------------------------------------------+ |          |
|            LAYER 2: MIDNIGHT WALLET            | |     ---->| dApp
|  Shielded     | Night (public) | Dust (fees)   | |          |
|  ZK proofs    | BIP-340 Schnorr| auto-regen    | |          |
+------------------------------------------------+ +----------+
|            LAYER 1: TEE (not exposed to dApps)              |
|  AES-256-GCM wrapping key | BIP39 seed | CIP-1852 derive   |
+-------------------------------------------------------------+
```

### dApp-wallet connection (Midnight Connection Protocol)

```
  dApp                      Wallet                   Chain
   |                          |                        |
   |  1. Discover wallets     |                        |
   |  (EIP-6963 events on     |                        |
   |   window.midnight)       |                        |
   |                          |                        |
   |  2. Request session ---->|                        |
   |     scopes: [shielded,   |                        |
   |      proof, credential]  |                        |
   |                          |                        |
   |  3. User approves ------>|                        |
   |     (progressive auth)   |                        |
   |                          |                        |
   |  4. Call circuit ------->|                        |
   |     (mn_callCircuit)     |                        |
   |                          |                        |
   |  5. Proof generating     |                        |
   |     (~18s, async)  <-----|                        |
   |     progress callbacks   |                        |
   |                          |                        |
   |  6. Proof complete       |  Submit tx ---------->|
   |                    <-----|                        |
   |  7. Confirmed      <----|  Block included <------|
```

### SDK onboarding wrapper

```typescript
// 5-line dApp integration
const result = await midnight.onboard({
  name: 'alice',
  dustSponsor: SPONSOR_KEY,
  recovery: { method: 'derec', helpers: 5, threshold: 3 }
});
// Returns: { wallets, name, session, recoveryStatus }
```

### Key recovery via DeRec

```
  Lost Device                   Helpers (3 of 5 needed)

  New phone installs app        Helper A    Helper B    Helper C
       |                          |            |            |
       |--- pair in recovery ---->|            |            |
       |--- mode (fresh keys) --->|            |            |
       |                          |            |            |
       |<--- share_A + proof -----|            |            |
       |<--- share_B + proof ----------------|            |
       |<--- share_C + proof -----------------------------|
       |
       | Verify Merkle proofs against committed root
       | Lagrange interpolation: recover AES key K
       | Decrypt: seed = AES-GCM-decrypt(K, ciphertext)
       |
       | Re-derive all 3 wallet layers from seed
       | Re-derive attestation tree leaves (same secret key)
       | Identity credentials survive (SBTs on-chain)
       | Register new device key to existing account
       |
       | DONE: Full access restored
```

### Name resolution (ENS-inspired)

```
  Client                    Registry              Resolver
    |                       (Compact)             (Compact)
    |                          |                      |
    | 1. Compute namehash     |                      |
    |    using persistentHash |                      |
    |    (Poseidon, not       |                      |
    |     keccak256)          |                      |
    |                         |                      |
    | 2. Query registry ------+----> Look up         |
    |                         |     resolver addr    |
    |                         |                      |
    | 3. Query resolver ------|--------------------->|
    |                         |                      |
    |    Returns:             |                      |
    |    +--------------------------------------------------+
    |    | addr()         -> UserAddress (Night)             |
    |    | shieldedAddr() -> ZswapCoinPublicKey (ZK-private) |
    |    | crossChain()   -> BTC/ETH/SOL via SLIP-44         |
    |    | profile()      -> avatar, bio, social links       |
    |    +--------------------------------------------------+
    |
    |    Privacy-aware: shielded addresses require
    |    ZK proof of authorization to resolve
```

### Identity credentials (Midnight-native attestation trees)

```
  ISSUANCE (one-time, off-chain)            VERIFICATION (per-use)

  zkMe verifies user                        User wants to access dApp
       |                                          |
       v                                          v
  Build Merkle tree leaf:               Provide as witness inputs:
  persistentHash(["age18:", sk])          - secret_key
       |                                  - merkle_proof (siblings)
       v                                          |
  Publish Merkle root                             v
  to Compact contract                    Circuit computes:
       |                                  1. leaf = persistentHash(
       v                                        ["age18:", sk])
  Give user:                              2. root = fold(leaf, proof)
  - secret key (in TEE)                   3. assert(root == on_chain)
  - Merkle proof                          4. nullifier = persistentHash(
  - SBT on-chain                                 ["nullf:", sk])
                                          5. assert(nullifier not used)
                                          6. Store nullifier on-chain
                                                  |
                                                  v
                                          Service sees ONLY:
                                          - nullifier (unlinkable)
                                          - "eligible: true/false"
                                          Service NEVER sees:
                                          - identity, age, name, etc.
```

### Credential lifecycle

```
  Issuance         Active           Expiration/Revocation
  --------         ------           ---------------------
  zkMe verifies -> SBT minted  --> blockTimeLt(expiresAt)?
  user off-chain   Merkle root      |
  builds tree      on-chain    No --+--> Proof fails
  publishes root   nullifiers       |
                   tracked     Yes -> Still active
                                    |
                   Revocation: issuer publishes new root
                   excluding user -> old proofs fail
                                    |
                   Re-verify: new zkKYC -> new leaf
                   new SBT -> back to Active
```

### Institutional recovery (enterprise)

One of 5 DeRec helpers is an institutional service running in a TEE. It holds one encrypted share. Release requires compliance officer approval, employee auth, and a 48-hour delay. The user still controls a majority --- 3 of 4 personal helpers can recover without the institution. The audit trail satisfies regulators.

### Production readiness

| Topic | Design Section | Summary |
|---|---|---|
| **Alternative entry points** | 5.11 | Deep link, NFC tap, manual 8-char code — same ECDH, different transport. Screen reader support. |
| **Onboarding resilience** | 6.5 | Checkpoint-resume after network loss. Proof server retry with backoff. Deferred zkKYC/DeRec/naming. |
| **Name anti-abuse** | 4.13 | Rate limits (3/day per key), renewal fees, homoglyph rejection (ENSIP-15), PoP for premium names. |
| **Emergency procedures** | 10.6 | 5 scenarios: helper compromise, zkMe shutdown, proof outage, registry bug, full network down. |
| **Multi-account** | 9.6 | CIP-1852 account index. Each account gets own name, wallets, credentials, helpers. Unlinkable. |
| **Privacy analytics** | 12.5 | On-chain increment-only counters per funnel stage. No PII. Opt-in local telemetry. |

### Security model

| ID | Severity | Finding | Mitigation |
|---|---|---|---|
| S-01 | **CRITICAL** | Seed exposure in app memory during derivation | Chain-level key caching, mlock, immediate zeroization |
| S-02 | HIGH | iOS P-256 cannot do BLS12-381 natively | AES-256-GCM symmetric wrapping key (not P-256 asymmetric) |
| S-03 | HIGH | QR protocol vulnerable to MITM/QRLJacking | Server-signed ECDH, pinned key, visual confirmation codes |
| S-04 | HIGH | Single seed = single point of failure | DeRec (3,5) threshold + BIP-85 compartmentalization |
| S-05 | MEDIUM | Proof generation latency (~18-21s) | Progress indicators; parallel proof generation where possible |
| S-06 | MEDIUM | BLS12-381 signing keys vulnerable to Shor | DeRec uses ML-KEM (post-quantum); monitor NIST PQ signatures |

Full security review: [10 cryptographic findings + 23 protocol findings](./docs/secure-onboarding-design.md#13-appendix-a-security-review-summary)
