# did:zkme (zkMe)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/zkMeLabs/zkme-did-method-spec) |
| Organization | zkMeLabs |
| DID Format | `did:zkme:testnet:0x2acE1D0d919293D10Ef7611bC768F5386d908fc2` |

## Overview
did:zkme is a DID method using Zetachain (EVM-compatible blockchain) as the verifiable data registry. The method-specific identifier is an Ethereum address or secp256k1 compressed public key. Currently operating on Zetachain Testnet. All DID operations (create, update, delete) require gas fees paid in AZETA tokens. The method uses a smart contract registry for DID document storage.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires Zetachain blockchain:
- Resolution fetches DID documents from Zetachain smart contract
- CRUD operations require AZETA tokens for gas
- Registry contract at zkme-did-registry repository
- secp256k1 cryptography for key generation and signing
- Only DID owner/controller can update or delete
- Currently testnet only (production readiness unclear)

### 2. Ecosystem
Small ecosystem. zkMeLabs provides reference implementations:
- zkme-did-registrar - DID registration
- zkme-did-resolver - DID resolution

Zetachain is an omnichain blockchain designed for cross-chain interoperability.

### 3. Stability
Testnet deployment suggests early-stage development. Dependency on Zetachain's continued operation and AZETA token economics. W3C Credentials Community Group alignment claimed.

## Recommendation
**No-go**

Requires Zetachain blockchain and AZETA tokens for all operations. Currently only deployed on testnet, suggesting the method is not production-ready.
