# MPC Candidates for Nightstream — Integration Analysis

## Constraints

The MPC protocol needs to do three jobs inside Nightstream's constraints:

1. **Key generation** — split a new key into shares at account creation
2. **Signing** — produce signatures without reconstructing the key
3. **Recovery** — reconstruct access when a device is lost

Nightstream's hard constraints:
- Goldilocks field (64-bit) — everything inside proofs runs here
- ~1 kHz proved execution rate
- 500MB - 2GB memory
- Must work in browser/mobile WASM containers

---

## The Candidates

### 1. Shamir Secret Sharing + on-device signing

**How it works:** Generate a full key, immediately split it into shares (polynomial evaluation), distribute shares to device TEE + server + recovery guardian. To sign, reconstruct the key briefly in the TEE, sign, destroy.

**Nightstream integration:**
- Shamir splitting/reconstruction is **just polynomial math over a field** — can run natively over Goldilocks. Extremely cheap to prove (~dozens of constraints).
- The signature itself happens in the TEE, not inside the proof system.

**The reconstruction concern:**

Shamir is a splitting and reconstruction scheme — it has no signing protocol. At signing time, shares must be combined to reconstruct the full key, which then signs:

```
Share 1 (device)  ──┐
Share 2 (server)  ──┼──→  Reconstruct full key  ──→  Sign  ──→  Destroy key
Share 3 (guardian) ─┘          ↑
                          Key is whole HERE
                          (even if briefly, in TEE memory)
```

Compare with threshold signing (FROST, CGGMP) where the key is **never** reconstructed:

```
Share 1 (device)  ──→  Partial signature 1  ──┐
Share 2 (server)  ──→  Partial signature 2  ──┼──→  Combine  ──→  Valid signature
                                               │
                    Full key NEVER exists anywhere
```

**Is this a real risk in practice?**

- **Acceptable for most use cases:** ZenGo, Web3Auth, Privy all use reconstruct-then-sign. Reconstruction happens inside a TEE (Secure Enclave) where memory extraction is extremely hard. The key exists for milliseconds, then is zeroed. Millions of users trust this model.
- **Weaker in theory:** TEEs have been broken before (SGX side-channel attacks, Spectre/Meltdown). "Never reconstructed" is a strictly stronger guarantee. Government/enterprise use cases may require the stronger property.

**Cost inside Nightstream:** Very low. Shamir over Goldilocks is native arithmetic (~dozens of constraints).

**Verdict:** Easiest to implement, cheapest to prove, industry-proven security model. The reconstruction weakness is theoretical and mitigated by TEE. Strong candidate for v1 with a clear upgrade path to threshold signing (FROST) for v2.

---

### 2. FROST (Flexible Round-Optimized Schnorr Threshold)

**How it works:** Threshold Schnorr signatures. Each party holds a key share. They collaboratively produce a valid Schnorr signature without any party ever seeing the full key. 2 rounds (or 1 with preprocessing).

**Nightstream integration:**
- FROST was designed for Schnorr, which works over elliptic curves (Ed25519 or secp256k1).
- Ed25519 uses Curve25519 — a 255-bit prime field. Inside Goldilocks, each field multiplication becomes ~16 Goldilocks multiplications + carries. An Ed25519 point addition is ~10 field muls → ~160 Goldilocks muls → ~hundreds of CCS constraints per point operation.
- A FROST signing round involves a few point multiplications and additions — maybe **~5,000-10,000 CCS constraints** for the in-circuit part.
- **But:** most of FROST happens between parties (communication rounds), not inside a proof. The question is what exactly needs to be proved. If you only need to verify the final Schnorr signature on-chain, that's one signature verification = ~10K-20K constraints. If you need to prove the MPC protocol itself ran correctly, that's much more.

**The problem:**
- Schnorr works for Bitcoin (taproot) but NOT for Ethereum (which uses ECDSA over secp256k1). So you'd need a different protocol for Ethereum chain signatures.
- Current Midnight uses Schnorr signatures, so there's natural alignment. But Nightstream is a new system — the signature scheme isn't locked yet.
- Ed25519 is non-native to Goldilocks. Not catastrophically expensive, but not cheap either.

**Could FROST work over Goldilocks natively?** Theoretically yes — you could define a Schnorr signature scheme directly over the Goldilocks field with an appropriate elliptic curve. But no standard curve exists for Goldilocks, and no ecosystem would recognize those signatures. You'd be signing things that only Midnight understands.

**Cost inside Nightstream:** Medium. ~10K-20K constraints for signature verification. More if proving the MPC protocol itself.

**Verdict:** Good fit for Schnorr-compatible chains (Bitcoin taproot). Clean protocol, well-studied. Non-native arithmetic is manageable but not free. Doesn't cover ECDSA (Ethereum).

---

### 3. GG20 / CGGMP (Threshold ECDSA)

**How it works:** Threshold ECDSA — the standard for institutional crypto custody. Each party holds a share, they collaboratively produce a valid ECDSA signature. Used by Fireblocks, Coinbase, ZenGo (partially).

**Nightstream integration:**
- ECDSA operates over secp256k1 (256-bit). Same non-native cost as FROST (~16x overhead per field mul).
- **But the killer:** GG20/CGGMP internally uses **Paillier encryption**, which operates over a 2048-bit RSA modulus. A single Paillier operation inside Goldilocks would be astronomically expensive — we're talking **millions of CCS constraints** for one encryption/decryption.
- Even if you only verify the final ECDSA signature (not the MPC protocol), you still pay ~20K constraints for secp256k1 verification.

**The problem:** If any part of the MPC protocol needs to be proved (not just the final signature), Paillier makes it infeasible inside Nightstream. The protocol would have to run entirely off-chain with only the result verified.

**Cost inside Nightstream:** Very high for the protocol itself (Paillier). Medium for final signature verification only (~20K constraints).

**Verdict:** Industry standard but terrible fit for in-circuit proving. Only viable if the MPC runs entirely outside the proof system and you just verify the output signature.

---

### 4. Schnorr/EdDSA multisig (not MPC, but simpler)

**How it works:** Not threshold signatures — just n-of-n or k-of-n multisig using Schnorr signature aggregation (MuSig2). Each party signs independently, signatures are aggregated.

**Nightstream integration:**
- Same non-native cost as FROST for Ed25519/secp256k1.
- But much simpler protocol — no Paillier, no complex MPC rounds.
- MuSig2 is 2 rounds, well-studied, used in Bitcoin taproot.

**The problem:** n-of-n only (everyone must sign). For k-of-n threshold, you need FROST. Also only works for Schnorr, not ECDSA.

**Cost inside Nightstream:** Medium. Similar to FROST.

**Verdict:** Simpler than FROST but less flexible. Good for 2-of-2 setups (device + server).

---

### 5. BLS threshold signatures

**How it works:** BLS signatures have a unique property — they're linearly aggregatable. A k-of-n threshold BLS signature is just Shamir secret sharing applied to BLS keys, with partial signatures combined via Lagrange interpolation. No complex MPC protocol needed.

**Nightstream integration:**
- BLS uses the BLS12-381 curve — a **381-bit** prime field, plus pairing operations over extension fields.
- A single BLS verification requires a pairing, which is ~10,000 field multiplications over a 381-bit field. Inside Goldilocks, that's roughly **hundreds of thousands of CCS constraints**.
- **However:** `neo-midnight-bridge` already has `midnight-curves` with BLS12-381 support. This suggests the team has already considered BLS for bridging between Nightstream and the current Midnight chain.

**The problem:** Pairings are extremely expensive inside any proof system. BLS verification is the most expensive option. But the threshold protocol itself is the simplest (just Shamir + Lagrange over the BLS scalar field).

**The upside:** BLS threshold signing is the cleanest threshold scheme — no complex interactive protocol, partial signatures are just BLS signatures that combine linearly. And Dfinity/Internet Computer already uses this at scale for "chain key" signatures.

**Cost inside Nightstream:** Very high for in-circuit verification (pairings). But the signing protocol itself is simple.

**Verdict:** Cleanest threshold protocol, worst in-circuit cost. Only viable if verification happens outside the proof system (on the base layer).

---

### 6. The hybrid approach (most likely practical answer)

**How it would work:** Don't prove the MPC itself inside Nightstream. Instead:

- **MPC runs off-chain** in the WASM container but NOT inside the proof system. The device, server, and guardian communicate to produce a signature using whatever protocol works best (FROST for Schnorr chains, CGGMP for ECDSA chains).
- **Only the result is verified on-chain** — the final signature is checked by Nightstream. This costs ~10K-20K constraints for secp256k1/Ed25519 signature verification.
- **Key generation uses Shamir** over Goldilocks for the Nightstream-native key, which is cheap to prove.
- **Chain signatures** (signing on Bitcoin/Ethereum) happen via MPC off-chain, with the result bridged.

This is actually how most production systems work today — ZenGo, Fireblocks, Privy don't prove the MPC protocol. They run it off-chain and just submit the resulting valid signature to the chain.

---

## Summary Table

| Candidate | Key gen cost | Signing cost (in-circuit) | Signing cost (off-chain + verify) | Recovery | Ethereum compatible | Bitcoin compatible |
|---|---|---|---|---|---|---|
| Shamir + TEE | Very low | N/A (TEE signs) | N/A | Simple (reconstruct) | Yes (any curve) | Yes |
| FROST | Medium | 5K-10K constraints | 10K-20K constraints | Complex (resharing) | No (Schnorr only) | Yes (taproot) |
| GG20/CGGMP | High | Infeasible (Paillier) | 10K-20K constraints | Complex | Yes (ECDSA) | Yes (ECDSA) |
| MuSig2 | Medium | Similar to FROST | 10K-20K constraints | Limited (n-of-n) | No (Schnorr only) | Yes (taproot) |
| BLS threshold | Medium | Very high (pairings) | Expensive verification | Simple (Shamir-based) | No | No |
| **Hybrid** | **Low (Shamir/Goldilocks)** | **N/A (off-chain)** | **10K-20K constraints** | **Flexible** | **Yes** | **Yes** |

---

## Assessment

The **hybrid approach** is almost certainly the right answer for the 10-day timeframe:

1. **Nightstream-native key:** Shamir over Goldilocks (cheap, native)
2. **Cross-chain signing:** FROST for Bitcoin, CGGMP for Ethereum — both running off-chain in the WASM container
3. **On-chain verification:** Only verify the final signature (~10K-20K constraints)
4. **Recovery:** Shamir share redistribution

### Phased upgrade path

| Phase | MPC approach | Security model | Nightstream cost |
|---|---|---|---|
| **v1 (launch)** | Shamir + TEE reconstruction | Key briefly whole in TEE (industry standard, ZenGo/Web3Auth/Privy model) | Very low (native Goldilocks) |
| **v2 (upgrade)** | FROST threshold signing | Key never reconstructed (strictly stronger) | Medium (non-native curve, ~10K-20K constraints) |

This is defensible: v1 uses a proven model trusted by millions of users, with an explicit upgrade path to a stronger guarantee. The Shamir share format is compatible with FROST — the same shares can be migrated to threshold signing without re-keying users.

### Open questions for IOHK MPC specialists

1. Is there a Schnorr-compatible elliptic curve that's efficient over Goldilocks (or its extension F_{p²})? If so, FROST could run natively with dramatically lower costs.
2. Are lattice-based signature schemes (Dilithium/ML-DSA) mature enough for a post-quantum native signature? They'd avoid non-native curve arithmetic entirely.
3. For cross-chain signing (Bitcoin/Ethereum), is it acceptable to run FROST/CGGMP entirely off-chain and only verify the resulting signature on-chain? What are the trust assumptions?
