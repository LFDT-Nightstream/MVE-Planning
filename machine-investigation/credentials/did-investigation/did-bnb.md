# did:bnb (BNB Smart Chain DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:bnb Spec](https://github.com/identity-com/did-bnb) |
| Organization | Identity.com / Ontology |
| DID Format | `did:bnb:[network:]<address>` |

## Overview

The did:bnb method provides decentralized identifiers on BNB Smart Chain (formerly Binance Smart Chain). It follows the EVM-compatible pattern similar to did:ethr, where any Ethereum-compatible address automatically becomes a valid DID.

Example DIDs:
- `did:bnb:0x1f4B9d871fed2dEcb2670A80237F7253DB5766De`
- `did:bnb:testnet:0x1f4B9d871fed2dEcb2670A80237F7253DB5766De`

### DID Structure

- **Prefix**: `did:bnb:`
- **Network** (optional): `testnet`, `devnet`, `localnet` (mainnet is default)
- **Address**: `0x` + 40 hex characters (Ethereum-style address)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generative - any public key is automatically a DID; `initializeDidState()` for on-chain features |
| Read | `resolveDidState(address)` queries smart contract |
| Update | `addVerificationMethod()`, `addService()`, `addController()`, etc. |
| Deactivate | Remove all verification methods |

### Supported Algorithms

- **secp256k1**: Primary, supports "Ownership Proof" flag
- **ed25519**: Supported, cannot have ownership proofs

### Contract Deployments

- Mainnet: `0x3e366D776150c63Eb53C6675734070696403BEe9`
- Testnet: `0x88a05b4370BbB90c9F3EEa72A65c77131a7bc18d`

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bnb method requires BNB Smart Chain:

- **EVM blockchain required**: All state stored on BSC
- **Smart contract dependency**: DID registry contract for advanced features
- **Generative aspect useful**: Basic DIDs work without transactions (like did:ethr)
- **On-chain updates**: All mutations require BSC transactions

**Adaptation possibility**: Very low. Same fundamental issue as did:ethr - we cannot verify BSC state without running BSC nodes or trusting external APIs.

The "generative DID" concept (any public key is automatically a valid DID) is interesting and mirrors did:key, but the advanced features (controllers, services, verification methods) all require blockchain state.

### 2. Ecosystem

**Moderate within BNB ecosystem**

- **Identity.com backing**: Established organization in identity space
- **BNB Chain integration**: Part of official BNB documentation
- **EVM compatibility**: Familiar patterns for Ethereum developers
- **Active development**: Recent specification and deployments

### 3. Stability

**Relatively stable**

- **Version 1.0**: Finalized specification
- **Deployed contracts**: Production deployments on mainnet/testnet
- **Ontology collaboration**: Joint specification effort
- **Standard patterns**: Follows established EVM DID patterns

## Special Considerations

- **Generative DIDs**: No transaction needed for basic DID - useful pattern
- **Ownership proof flag**: Can prove key ownership on-chain
- **Recovery keys**: Verification methods can be marked as recovery keys
- **Fast finality**: BSC provides quick transaction confirmation

## Recommendation

**No-go**

The did:bnb method is unsuitable for our use case:

1. **BNB Smart Chain dependency**: Requires BSC for state management
2. **Same issues as did:ethr**: Cannot verify EVM state from our ledger
3. **Limited unique value**: Similar to did:ethr, just on different chain

The generative DID concept (any secp256k1 key is automatically a DID without registration) is worth noting - this is similar to did:key and could inform our design. But the BNB-specific implementation isn't useful for us.
