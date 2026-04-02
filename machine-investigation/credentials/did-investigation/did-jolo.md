# did:jolo (Jolocom)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/jolocom/jolo-did-method/blob/master/jolocom-did-method-specification.md) |
| Organization | Jolocom |
| DID Format | `did:jolo:<keccak256_hash>` |

## Overview
Jolocom DID is an Ethereum-based decentralized identifier method that uses smart contracts for DID-to-IPFS mapping. The identifier is derived from the keccak256 hash of an Ethereum public key. DID documents are stored on IPFS, with the mapping maintained by an Ethereum smart contract.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Ethereum blockchain for smart contract operations
- Smart contract maintaining DID-to-IPFS hash mapping
- IPFS network for DID document storage and retrieval
- `getRecord` function queries to resolve DIDs
- `setRecord` function calls to create, update, or delete DIDs

Both Ethereum and IPFS infrastructure are essential for operation.

### 2. Ecosystem
Jolocom is an established self-sovereign identity project with a focus on privacy and user control. The project has been active in the European SSI ecosystem and has partnerships in various sectors.

### 3. Stability
The specification is well-documented with clear CRUD operations. The reliance on Ethereum provides blockchain stability, but also inherits Ethereum's gas costs and scalability limitations.

## Recommendation
**No-go**

Requires both Ethereum blockchain (for registry) and IPFS (for document storage). Dual infrastructure dependency makes self-contained operation impossible.
