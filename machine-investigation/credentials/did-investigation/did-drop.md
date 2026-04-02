# did:drop (Drone Delivery DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:drop Spec](https://tenman1739-alt.github.io/did-drop-spec/) |
| Organization | Unknown |
| DID Format | `did:drop:<AppID>:<MissionID>` |

## Overview

The did:drop method is designed for verifiable autonomous drone delivery and geospatial logistics. It uses Algorand blockchain for on-chain pointers and IPFS/Arweave for off-chain mission plan documents.

Example DID: `did:drop:4839201:ORDER-9876-ALPHA`

### DID Structure

- **Prefix**: `did:drop:`
- **AppID**: Algorand smart contract application ID
- **MissionID**: Unique identifier (hash or UUID)

### Hybrid Architecture

- **On-chain (Algorand)**: CID pointer in Box Storage
- **Off-chain (IPFS/Arweave)**: Full DID Document with mission data

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate signed document, upload to IPFS/Arweave, store CID on Algorand |
| Read | Parse DID → query Algorand Box → retrieve CID → fetch document → verify |
| Update | Generate new document, upload, overwrite CID pointer |
| Deactivate | Delete Box record or destroy registry contract |

### Security Features

- **Mission Authorization Token (MAT)**: Required for coordinate access
- **Dual key types**: Algorand keys (on-chain) + document signing keys (payload)
- **Ed25519**: Document signatures

## Evaluation

### 1. Feasibility/Complexity

**Not applicable to our use case**

The did:drop method is designed for a completely different purpose:

- **Drone missions**: Identifies delivery missions, not entities
- **Geospatial focus**: Contains flight coordinates and logistics
- **Algorand dependency**: Requires Algorand blockchain
- **IPFS/Arweave**: Off-chain storage required

**Adaptation possibility**: None. This solves a different problem (drone delivery coordination).

### 2. Ecosystem

**Niche / Domain-specific**

- **Drone delivery**: Very specific use case
- **Algorand ecosystem**: Limited to Algorand users
- **Active development**: v2.0 specification
- **Unknown organization**: Limited visibility

### 3. Stability

**Active but specialized**

- **v2.0**: Mission Plan Architecture
- **GDPR considerations**: Privacy-aware design
- **Domain-specific**: Unlikely to gain broad adoption

## Special Considerations

### Interesting Patterns

- **Hybrid on/off-chain**: Minimal on-chain footprint
- **CID pointers**: Content-addressed off-chain storage
- **Authorization tokens**: Access control for coordinates

### Use Case Mismatch

This method identifies drone missions and flight plans, not identities or entities.

## Recommendation

**No-go**

The did:drop method is unsuitable for our use case:

1. **Different purpose**: Designed for drone delivery coordination
2. **Algorand dependency**: Requires Algorand blockchain
3. **Domain-specific**: Not a general-purpose identity method
4. **IPFS/Arweave required**: Additional infrastructure dependencies

This solves a completely different problem than what we need.
