# did:midnight (Midnight)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/IAMXID/did-midnight/blob/main/Midnight_DID_method.md) |
| Organization | IAMX AG (Switzerland) |
| DID Format | `did:midnight:<uuid_v4>` |

## Overview
Midnight DID is a blockchain-based decentralized identifier method for the Midnight ledger. DIDs use UUID v4 format and are stored as transaction metadata on the Midnight blockchain. The method uses BIP32-Ed25519 for key generation and supports multiple verification methods including `publicKeyHex` and `AdaAddress` encoding.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Midnight blockchain for DID storage and resolution
- `didVerify` function on Midnight nodes
- Transaction metadata storage for DID documents
- Querying multiple nodes for trustworthiness verification

The specification explicitly designates "mainnet" as the network identifier, tying the method to Midnight infrastructure.

### 2. Ecosystem
Midnight is a privacy-focused blockchain project associated with the Cardano ecosystem (note: AdaAddress encoding support). IAMX AG (Switzerland) provides the DID specification. The privacy features (no personal data on-chain) align with GDPR considerations.

### 3. Stability
Early stage specification (draft v0.1). The reliance on Midnight blockchain ties the method's stability to that platform's development trajectory. Lifecycle tracking (created, registered, updated, deactivated timestamps) indicates operational maturity considerations.

## Recommendation
**No-go**

Requires Midnight blockchain infrastructure for all operations. Early stage specification with blockchain-specific dependencies.
