# did:iwt (InfoWallet)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/infowallet/did_method/blob/master/did_method.md) |
| Organization | InfoWallet |
| DID Format | `did:iwt:<base58_id>` |

## Overview
InfoWallet DID is a blockchain-based decentralized identifier method where DID documents are generated and managed through smart contract code. The method uses ECDSA Koblitz signatures for verification and implements nonce-based replay attack prevention through on-chain storage.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Blockchain network for smart contract execution
- Smart contracts for DID document generation and authority checks
- On-chain nonce storage for replay attack prevention
- RESTful API endpoints for CRUD operations

All DID operations are performed through smart contract interactions on the blockchain.

### 2. Ecosystem
Limited information available about ecosystem adoption. The specification emphasizes privacy by design, stating that the blockchain contains no PII (Personally-Identifiable Information).

### 3. Stability
The specification provides standard CRUD operations through RESTful APIs. Two creation methods are supported (simple and full type). However, the ecosystem appears to be small with limited external validation.

## Recommendation
**No-go**

Requires blockchain infrastructure with smart contracts for all DID operations. Cannot function independently without the underlying blockchain network.
