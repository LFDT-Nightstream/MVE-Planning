# did:pid (ProofID DID Method)

| Property | Value |
|----------|-------|
| Specification | [Link](https://github.com/proof-id/pid-did-driver/blob/main/did-method-specification.md) |
| Organization | proof-id (ProofID) |
| DID Format | `did:pid:<ss58-address>` |

## Overview
The ProofID DID Method (did:pid) is a decentralized identity system built on the ProofID blockchain. DIDs are created by encoding the signing public key of a ProofID Identity into an SS58 public address format. The method stores DID items on-chain as mappings linking a DID owner's public address to their signing key, encryption key, and an optional document reference location.

The system supports two resolution modes:
1. **Static**: Query the blockchain for a document reference, then fetch the DID Document from that external location
2. **Dynamic**: Generate DID Documents on-demand from on-chain data

DID Documents include Ed25519 signing keys, X25519 encryption keys, authentication properties, and service endpoints for messaging.

## Evaluation

### 1. Feasibility/Complexity
**Cannot support without external dependencies.**

Resolution requires access to the ProofID blockchain to query on-chain DID items. Additionally, the static resolution mode requires HTTP calls to fetch DID Documents from external storage locations referenced on-chain. Even the dynamic mode requires querying blockchain state to retrieve keys and generate documents. The ProofID blockchain is currently a proof-of-authority network (with plans to transition to permissionless), representing a specialized infrastructure dependency.

### 2. Ecosystem
**Limited ecosystem.**

- Maintained by the proof-id organization on GitHub
- Listed in the W3C DID Method Registry
- Has a JavaScript SDK (`pid-js-lib`) for DID operations
- The ProofID blockchain appears to be a niche/specialized network
- Limited evidence of widespread adoption or third-party tooling
- Specification version is 0.1, indicating early stage development

### 3. Stability
**Early stage / uncertain stability.**

- Specification version 0.1 suggests an immature spec
- Blockchain is transitioning from proof-of-authority to permissionless, indicating ongoing infrastructure changes
- Limited commit history and community activity visible on GitHub
- No major enterprise adopters identified

## Special Considerations
- Uses SS58 address encoding (common in Substrate-based chains)
- Requires both signing keys (Ed25519) and encryption keys (X25519)
- Document storage can be off-chain with on-chain references
- The CRUD operations depend entirely on blockchain transactions
- Security model relies heavily on private key management and blockchain consensus

## Recommendation
**No-go**

The did:pid method requires access to the ProofID blockchain for all resolution operations. Nodes would need to either run ProofID blockchain infrastructure or make HTTP calls to ProofID nodes to resolve DIDs. The static resolution mode additionally requires HTTP calls to external document storage locations. Neither approach is compatible with the constraint that nodes cannot make HTTP requests to validate DIDs and cannot access external blockchain state. The limited ecosystem and early-stage specification further reduce the value proposition of investing in workarounds.
