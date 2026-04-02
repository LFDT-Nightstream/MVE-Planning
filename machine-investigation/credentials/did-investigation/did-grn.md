# did:grn (Grano)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/EG-easy/grano-did/blob/main/did-method-specification.md) |
| Organization | EG-easy |
| DID Format | `did:grn:grano1<38-hex-characters>` |

## Overview
The did:grn method is built on the Grano blockchain network, using smart contracts for DID registry management. DIDs are composed of a grano address prefixed with "grano1". The method supports implicit registration (no network interaction required for DID creation), controller delegation, and expiring attributes. The specification deliberately restricts PII storage to prevent misuse.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution is entirely blockchain-dependent, requiring queries to the Grano contract to retrieve controller addresses and enumerate contract events (DIDControllerChanged, DIDAttributeChanged). The DID document must be reconstructed by processing events chronologically from the blockchain.

### 2. Ecosystem
Very small ecosystem centered around the EG-easy organization. The project includes supporting components (resolver, client library, exporter, node software) but appears to have limited adoption outside its original development context.

### 3. Stability
The specification is documented with clear CRUD operations and security considerations. However, the Grano blockchain itself is not widely known, raising concerns about network longevity and validator availability.

## Recommendation
**No-go**

Requires access to the Grano blockchain network for DID resolution. Node operators cannot verify DIDs without querying the Grano smart contract and processing blockchain events.
