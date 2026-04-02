# did:rm (RealMatter)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/realmatterio/RealMatter-DID-Method) |
| Organization | RealMatter (Ming-lam Ng) |
| DID Format | `did:rm:<ledger>:<40-hex-chars>` |

## Overview
did:rm is a DID method for RealMatter's virtual assets platform. Similar to did:resume, it uses hardware-generated public keys for identifier creation. The method was officially registered with W3C on January 25, 2023.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires external infrastructure:

- DIDs anchored to distributed ledger technology (DLT)
- Enecuum ENQ public blockchain mentioned as ledger option
- HTTP REST operations via `virtualassets.id` endpoint
- Hardware security key storage for private keys
- Uses EcdsaSecp256k1 signatures

### 2. Ecosystem
**Very small.** Focused on RealMatter/virtual assets use case. Limited documentation and adoption.

### 3. Stability
**Early stage.** W3C registered but limited production evidence. Specification tied to specific platform infrastructure.

## Recommendation
**No-go**

Requires both blockchain infrastructure (Enecuum or similar) and HTTP endpoints (`virtualassets.id`) for operation. Multiple external dependencies make it unsuitable for self-contained use.
