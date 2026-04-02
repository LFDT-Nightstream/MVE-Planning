# did:psi (Police Science Institution)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/didpsi/psi-DID/blob/main/docs/did:psi-method-spec_eng.md) |
| Organization | Police Science Institution (Korea) |
| DID Format | `did:psi:<21-22 base58 chars>` |

## Overview
did:psi is a DID method developed by the Police Science Institution in Korea for decentralized identity management in public safety research contexts. It uses base58-encoded identifiers (excluding confusing characters) and EcdsaSecp256k1 verification keys.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires blockchain infrastructure:

- DID Documents stored in blockchain "did table"
- UUIDs converted to BigInt for efficient blockchain storage
- All CRUD operations execute through smart contract actions
- Dedicated resolver converts blockchain data to W3C-compliant format

Operations include regdid (create), updatekeys, addauth/rmauth, and deletedid actions.

### 2. Ecosystem
**Very small.** Appears to be primarily used within Korean public safety research contexts. Limited documentation and community activity.

### 3. Stability
**Early stage.** Version 0.0.1 indicates early development. Focused on specific use case (public safety research).

## Recommendation
**No-go**

Requires blockchain infrastructure for all DID operations. The smart contract dependency makes it unsuitable for self-contained ledger operation.
