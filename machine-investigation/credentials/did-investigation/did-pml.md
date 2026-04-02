# did:pml (Purple Mountain Laboratories)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/PML-ID/pml-did-specs/blob/main/did-method.md) |
| Organization | Purple Mountain Laboratories |
| DID Format | `did:pml:<method-specific-id>` |

## Overview
did:pml is a DID method developed by Purple Mountain Laboratories supporting two identifier types: cryptographic (derived from public key hash) and semantic (human-readable identifiers like domain names). It uses secp256k1 cryptography and self-signed proofs for document verification.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** Despite claiming no blockchain dependency, the method requires a centralized HTTP resolver service at `https://did.pmlabs.com/v1/did/resolve/` for all CRUD operations. DID documents are stored and retrieved from this central service rather than being self-verifiable.

Key characteristics:
- No blockchain, but centralized resolver dependency
- Self-signed proofs using EcdsaSecp256k1VerificationKey2019
- Private keys remain on user devices

### 2. Ecosystem
**Small.** Limited adoption outside of Purple Mountain Laboratories' projects. Minimal community activity and documentation.

### 3. Stability
**Early stage.** The specification appears to be in active development with limited production deployments.

## Recommendation
**No-go**

While did:pml avoids blockchain dependency, it replaces it with a centralized HTTP resolver service, which still requires external infrastructure for resolution. Not suitable for self-contained operation.
