# did:itn (Integrated Trust Network)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/itn-trust/itn-did-spec) |
| Organization | Integrated Trust Network (ITN) |
| DID Format | `did:itn:<base58_encoded_id>` |

## Overview
ITN DID is a decentralized identifier method built on dual ledger infrastructure: Hyperledger Fabric and Arbitrum One (EVM). The identifier is derived from the first 16 bytes of a recovery key pair's public key, encoded in Base58. DID documents are stored in a federated Content Addressable Storage (CAS) system with replication across ITN nodes.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Hyperledger Fabric network for transaction recording
- Arbitrum One EVM network for additional anchoring
- Federated Content Addressable Storage (CAS) for document storage
- DIF resolver infrastructure integration
- OAuth2 and DID AuthN for authentication

The dual-ledger design provides redundancy but significantly increases infrastructure dependencies.

### 2. Ecosystem
Community-built network with notable contributors. The specification includes DIDComm 2.0 messaging support and Encrypted Data Vault implementation, indicating alignment with modern DID standards.

### 3. Stability
The specification supports five core operations (Create, Resolve, Update, Revoke, Recover) with W3C standards compliance. However, the dual-ledger architecture and federated storage introduce complexity and potential points of failure.

## Recommendation
**No-go**

Requires two separate blockchain networks (Hyperledger Fabric and Arbitrum One) plus federated storage infrastructure. Extremely high infrastructure dependency.
