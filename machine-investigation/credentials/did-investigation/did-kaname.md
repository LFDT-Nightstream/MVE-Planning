# did:kaname (Kaname Protocol)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/KanameProtocol/did-kaname-spec/blob/main/README.md) |
| Organization | Kaname Protocol / Kaname Foundation |
| DID Format | `did:kaname:<namespace>:<member>` |

## Overview
Kaname DID is a decentralized identifier method for EVM-compatible blockchains. DIDs are automatically created when smart contracts are deployed, with identifiers tied to contract and wallet addresses (CAIP-10 compliant). The method uses EcdsaSecp256k1RecoveryMethod2020 for verification.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- EVM-compatible blockchain networks (Ethereum mainnet, etc.)
- Kaname Foundation's public API endpoints for resolution
- HTTP requests to `https://kaname.io/did/v1/read/` for document retrieval
- On-chain smart contract lookups

Resolution explicitly depends on both blockchain state and Kaname Foundation API services.

### 2. Ecosystem
Focused on EVM ecosystem integration. The specification notes that update and deactivation operations are currently unsupported, indicating an early development stage.

### 3. Stability
Early stage specification with limited functionality (no update/deactivate). The reliance on Kaname Foundation's centralized API endpoint creates a potential single point of failure despite the decentralized blockchain backend.

## Recommendation
**No-go**

Requires EVM blockchain infrastructure and Kaname Foundation API services. The combination of blockchain dependency and centralized API creates dual infrastructure requirements.
