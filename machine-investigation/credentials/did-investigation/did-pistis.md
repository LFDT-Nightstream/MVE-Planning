# did:pistis (Pistis DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:pistis Spec](https://github.com/uino95/ssi/blob/consensys/dashboard/server/pistis/pistis-did-resolver/README.md) |
| Organization | uino95 / ConsenSys |
| DID Format | `did:pistis:<ethereum-address>` |

## Overview

The did:pistis method is a credential management system based on the Ethereum blockchain, implementing decentralized identifiers conforming to W3C DID specifications. It provides smart contracts for multi-signature operations, delegate management, and permissioned access to extensible services.

Example DID:
- `did:pistis:0xf3beac30c498d9e26865f34fcaa57dbb935b0d74`

### DID Structure

- **Prefix**: `did:pistis:`
- **Identifier**: An Ethereum address (40 hexadecimal characters)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Offline key generation using libsecp256k1 elliptic curve cryptography, deriving Ethereum address from public key via keccak-256 hashing |
| Read | On-chain resolution via PistisDidRegistry smart contract |
| Update | Multi-signature operations for key rotation and delegate management |
| Deactivate | Revocation of all authorized addresses |

### Resolution Process

The Pistis-Did-Resolver (Universal Resolver implementation) retrieves DID Documents from smart contracts, returning a document containing:
- Public keys
- Delegate management references
- Status registry permissions
- Service endpoints

### Smart Contracts

Key contracts deployed on Ethereum (documented on Ropsten testnet, network ID: 3):
- PistisDidRegistry
- MultiSigOperations
- CredentialStatusRegistry

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:pistis method requires Ethereum blockchain access:

- **Smart contract queries**: Resolution requires calling PistisDidRegistry contract
- **Multi-signature operations**: Updates require on-chain transactions
- **Ethereum dependency**: All CRUD operations except create require blockchain state
- **Testnet focus**: Primary documentation references Ropsten testnet (now deprecated)

**Generative aspect interesting:**
Like did:ethr, DIDs can be created offline by generating a keypair and deriving the Ethereum address. However, all features beyond basic creation require blockchain state.

**Adaptation possibility**: Very low. We cannot verify Ethereum state from our ledger without:
1. Running Ethereum nodes
2. Implementing cross-chain proofs
3. Trusting external APIs (centralization)

### 2. Ecosystem

**Limited / Research project**

- **Single repository**: Maintained in uino95/ssi GitHub repository
- **ConsenSys connection**: Some deployment infrastructure
- **Limited adoption**: No evidence of widespread production use
- **Universal Resolver**: Reference implementation exists
- **Documentation**: Specification at v1.0 but minimal ecosystem tooling

### 3. Stability

**Unstable / Experimental**

- **Version 1.0**: Specification exists but appears to be academic/research
- **Ropsten testnet**: Primary documentation references deprecated test network
- **Maintenance status**: Repository appears to have limited recent activity
- **No W3C registration**: Not registered in W3C DID Method Registry

## Special Considerations

### Similarities to did:ethr

The did:pistis method shares many characteristics with did:ethr:
- Ethereum address as identifier
- secp256k1 key generation
- Smart contract-based registry
- Event-based state management

### Unique Features

- **Multi-signature operations**: Built-in support for multi-sig governance
- **Credential status registry**: Integrated revocation infrastructure
- **Permissioned services**: Access control for extensible service endpoints

### Limitations

- **Ropsten deprecated**: Primary test network no longer exists
- **Limited documentation**: Specification less comprehensive than did:ethr
- **Single implementation**: No alternative implementations
- **Academic focus**: Appears to be research/proof-of-concept rather than production system

## Recommendation

**No-go**

The did:pistis method cannot be supported for multiple reasons:

1. **Ethereum dependency**: Requires querying Ethereum blockchain state for resolution
2. **Cannot verify on-ledger**: Our nodes cannot access Ethereum smart contract state
3. **Limited ecosystem**: Minimal adoption and tooling compared to alternatives
4. **Unstable infrastructure**: Primary testnet (Ropsten) has been deprecated
5. **did:ethr overlap**: Provides similar functionality to did:ethr with less ecosystem support

If Ethereum-based DIDs are needed, did:ethr would be the more appropriate choice due to its wider adoption, better documentation, and active maintenance - though it also faces the same fundamental blocker of requiring external blockchain access.
