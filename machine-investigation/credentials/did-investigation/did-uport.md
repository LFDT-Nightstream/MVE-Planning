# did:uport (uPort)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/uport-project/specs) |
| Organization | uPort / ConsenSys |
| DID Format | `did:uport:<ethereum-address>` (deprecated in favor of did:ethr) |

## Overview
did:uport was an early Ethereum-based DID method developed by uPort (ConsenSys). The project has evolved and transitioned to did:ethr (Ethr-DID), which conforms to ERC-1056. The uPort platform consists of a mobile app and Ethereum smart contracts (Proxy contracts) for identity management. The current recommended approach is to use did:ethr rather than the original did:uport method.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** The method depends on Ethereum infrastructure:
- Requires Ethereum-compatible blockchain for resolution
- Uses ERC-1056 smart contract registry (ethr-did-registry)
- DID operations require Ethereum transactions
- Resolution requires access to Ethereum nodes
- Mobile app integration for identity management

### 2. Ecosystem
The uPort ecosystem was influential in early DID development. The project evolved into Veramo (DID framework) and Serto. The did:ethr method has reasonable adoption within the Ethereum identity community. Well-documented with active GitHub repositories.

### 3. Stability
The original did:uport is deprecated. The successor did:ethr is more mature and actively maintained. W3C compliant through the Credentials Community Group process.

## Recommendation
**No-go**

Requires Ethereum blockchain infrastructure. The method is deprecated in favor of did:ethr, which also requires Ethereum.
