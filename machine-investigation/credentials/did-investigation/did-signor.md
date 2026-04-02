# did:signor (Signor)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/cryptonicsconsulting/signor-did-contracts/blob/master/did-method-spec.md) |
| Organization | Cryptonics Consulting |
| DID Format | `did:signor:<network>:<ethereum-address>` |

## Overview
did:signor is a DID method using Ethereum smart contracts (DIDRegistry) for identity management. Supports multiple Ethereum networks (mainnet, ropsten, rinkeby, kovan). The Ethereum address serves as the identifier.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Ethereum blockchain:

- DIDs registered in DIDRegistry smart contract on-chain
- On-chain structure: controller address, created/updated timestamps
- Create via `createDID(address subject)` transaction
- Update via `setController()` - controller only
- Delete via `deleteDID()` - controller only
- DID documents dynamically constructed from ledger data

### 2. Ecosystem
**Small.** Focused on Cryptonics ecosystem. Basic documentation and reference implementation.

### 3. Stability
**Moderate.** Clear specification following W3C DID Core. Simple contract design.

## Recommendation
**No-go**

Requires Ethereum blockchain infrastructure for all DID operations. Cannot function without access to supported Ethereum networks.
