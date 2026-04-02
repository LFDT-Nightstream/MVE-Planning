# did:factom (Factom DID Method)

| Property | Value |
|----------|-------|
| Specification | [Factom DID Spec](https://github.com/factom-protocol/FIS/blob/master/FIS/DID.md) |
| Organization | Factomatic LLC, Sphereon BV, Factom Inc |
| DID Format | `did:factom:[network:]<64-char-chain-id>` |

## Overview

The did:factom method stores DID documents as entries within Factom chains. The chain ID serves as the persistent identifier, and operations create immutable entries in the chain.

Example DIDs:
- `did:factom:f26e1c422c657521861ced450442d0c664702f49480aec67805822edfcfee758`
- `did:factom:mainnet:f26e1c422c657521861ced450442d0c664702f49480aec67805822edfcfee758`

### DID Structure

- **Prefix**: `did:factom:`
- **Network** (optional): mainnet, testnet
- **Chain ID**: 64-character hex-encoded Factom chain ID

### Key Features

- **Chain-based storage**: Each DID has its own chain
- **Immutable entries**: Operations stored as entries
- **Priority-based keys**: Management key hierarchy
- **Version upgrades**: Method version can be upgraded

### CRUD Operations

| Operation | Tagged Entry |
|-----------|--------------|
| Create | "DIDManagement" - first entry with keys/services |
| Read | Retrieve valid document, supports versioning |
| Update | "DIDUpdate" - signed modifications |
| Deactivate | "DIDDeactivation" - permanent deactivation |
| Upgrade | "DIDMethodVersionUpgrade" - version change |

### Supported Key Types

- Ed25519VerificationKey
- ECDSASecp256k1VerificationKey
- RSAVerificationKey

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:factom method requires:

- **Factom blockchain**: All operations on Factom network
- **Entry Credits**: Factom's payment mechanism for entries
- **Chain management**: Each DID is a Factom chain
- **Resolver access**: Must query Factom for resolution

**Adaptation possibility**: Low. Deeply integrated with Factom's chain model.

### 2. Ecosystem

**Niche / Factom ecosystem**

- **Multiple contributors**: Factomatic, Sphereon, Factom Inc
- **Draft status**: Version 1.0 draft
- **Factom users**: Limited to Factom ecosystem
- **Enterprise focus**: Data integrity use cases

### 3. Stability

**Draft but complete**

- **Well-documented**: Comprehensive specification
- **Multi-stakeholder**: Several organizations involved
- **Factom dependency**: Tied to Factom blockchain success

## Special Considerations

### Interesting Patterns

- **Priority-based key hierarchy**: Management key recovery
- **Version upgrades**: Method specification versioning
- **Immutable audit trail**: All operations recorded
- **Resolution parameters**: version-id, version-time support

### Limitations

- **Factom-specific**: Chain model unique to Factom
- **Entry Credits**: Payment required for operations
- **Blockchain dependency**: Cannot operate independently

## Recommendation

**No-go**

The did:factom method is unsuitable for our use case:

1. **Factom dependency**: Requires Factom network access
2. **Entry Credits**: Payment mechanism required
3. **External chain state**: Cannot verify from our ledger
4. **Niche ecosystem**: Limited adoption outside Factom

The priority-based key management is a good pattern but the Factom dependency is prohibitive.
