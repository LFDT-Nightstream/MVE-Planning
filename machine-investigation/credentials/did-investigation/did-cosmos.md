# did:cosmos (Cosmos DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:cosmos Spec](https://github.com/EarthProgram/did-cosmos) |
| Organization | EarthProgram (ixo, Regen) |
| DID Format | `did:cosmos:[version]:[chainspace]:[namespace]:[unique-id]` |

## Overview

The did:cosmos method is designed for the Cosmos blockchain ecosystem, allowing DIDs to work across multiple Cosmos SDK-based chains. It uses the Chain Registry to map chainspace identifiers to specific chains.

Example DID: `did:cosmos:ixo:nft:abc123...`

### DID Structure

- **Version** (optional): Integer, defaults to 1
- **Chainspace** (required): Identifies Cosmos chain (e.g., "ixo", "regen", "cosmos")
- **Namespace** (optional): Asset module (e.g., "nft", "bank")
- **Unique-id** (optional): Secp256k1 public key in multibase/multicodec format

### Architecture

Three core components:
1. **Chain Registry**: Maps chainspace to chain descriptors
2. **Namespace Registry**: Maps namespace to asset module descriptors
3. **Asset Module**: Manages on-chain assets with CRUD functions

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Asset module creates asset with signature |
| Read | Resolve from chain or generate deterministically |
| Update | Asset module updates asset |
| Deactivate | Asset module deactivates asset |

### Resolution Process

1. Check on-chain for registered identifier
2. If not found, generate minimalist deterministic document (like did:key)
3. Preserve full did:cosmos identifier

### Supported Algorithms

- Secp256k1 (encoded with multibase/multicodec)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:cosmos method requires:

- **Cosmos SDK chains**: Must access specific Cosmos chains
- **Chain Registry**: Depends on Cosmos Chain Registry
- **Multi-chain complexity**: Designed for cross-Cosmos resolution
- **Asset modules**: Requires chain-specific module implementations

**Adaptation possibility**: Low. While the fallback to did:key-style resolution is interesting, the primary use case requires Cosmos chain access.

### 2. Ecosystem

**Cosmos ecosystem**

- **EarthProgram**: ixo, Regen network backing
- **Cosmos SDK**: Part of broader Cosmos ecosystem
- **Active development**: Under development
- **IID extensions**: Custom JSON-LD extensions

### 3. Stability

**Active development**

- **Under development**: Not finalized
- **Version support**: Plans for backward compatibility
- **Cosmos backing**: Strong ecosystem support

## Special Considerations

### Interesting Features

- **Fallback resolution**: Generates did:key-style document if not on-chain
- **Linked resources**: Privacy-respecting resource attachments
- **Transcludes**: Embed linked resources in documents
- **Offline creation**: Supports offline DID creation before registration

### Multi-chain Design

- **Chainspace**: Identifies which Cosmos chain
- **Namespace**: Identifies which asset module
- **Cross-chain resolution**: Designed for multi-chain ecosystem

## Recommendation

**No-go**

The did:cosmos method is unsuitable for our use case:

1. **Cosmos dependency**: Requires access to Cosmos SDK chains
2. **Chain Registry**: Depends on external chain registry
3. **Multi-chain complexity**: Designed for Cosmos ecosystem navigation
4. **External state**: Cannot verify Cosmos chain state from our ledger

The fallback to deterministic resolution (like did:key) is a good pattern, but we can just use did:key directly for that purpose.
