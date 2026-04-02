# did:san (SAN/Baasze)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/Baasze/DID-method-specification) |
| Organization | YLZ Inc. |
| DID Format | `did:san:<12-char-id>` |

## Overview
did:san is a DID method for the SAN blockchain, which uses Byzantine Fault Tolerance-DPoS consensus. The method-specific identifier consists of 12 characters (a-z, 1-5). Supports both ECDSA secp256k1 and SM2 cryptographic algorithms.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires SAN blockchain infrastructure:

- Uses BFT-DPoS consensus protocol
- HTTP resolution via `https://did.baasze.com/v1/did/resolve/`
- All CRUD operations through HTTP endpoints
- Supports EcdsaSecp256k1Signature2019 as default key type

### 2. Ecosystem
**Very small.** Appears to be a regional project. Limited documentation and unclear adoption status.

### 3. Stability
**Moderate.** Follows W3C DID Core v1.0 specification. Documentation is basic but complete.

## Recommendation
**No-go**

Requires SAN blockchain and HTTP resolver endpoints for all operations. Not suitable for self-contained use.
