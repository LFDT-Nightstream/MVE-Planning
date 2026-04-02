# did:tys (Trust Your Supplier)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/chainyard-tys/tys/blob/master/README.md) |
| Organization | Chainyard |
| DID Format | `did:tys:<base58-encoded-id>` |

## Overview
did:tys is a DID method for Trust Your Supplier (TYS), a supply chain identity platform on Hyperledger Fabric. The identifier is base58-encoded from SHA-256 hashed ECDSA public key material. Designed for supplier identity management and credential sharing.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Hyperledger Fabric infrastructure:

- Implemented on Hyperledger Fabric permissioned blockchain
- Smart contracts written in Golang
- Certificate-based authentication
- Consensus via Kafka (transitioning to RAFT)
- Credential references and entity type classification (SUPPLIER)
- Cryptographic signatures required for write operations

### 2. Ecosystem
**Enterprise-focused.** Part of IBM's blockchain initiatives. Supply chain management focus. Limited to TYS consortium participants.

### 3. Stability
**Enterprise-grade.** Production deployment for supply chain use cases. Well-documented enterprise governance model.

## Recommendation
**No-go**

Requires Hyperledger Fabric blockchain infrastructure and consortium membership. Not suitable for self-contained or publicly accessible use.
