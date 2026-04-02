# did:elem (Element DID Method) - ARCHIVED

| Property | Value |
|----------|-------|
| Specification | [Element Spec](https://github.com/decentralized-identity/element/blob/master/docs/did-method-spec/spec.md) |
| Organization | Decentralized Identity Foundation |
| DID Format | `did:elem:[network:]<sidetree-hash>` |
| Status | **ARCHIVED - February 17, 2021** |

## Overview

Element was a Sidetree protocol implementation using Ethereum as the ledger layer and IPFS for content-addressable storage. The project has been archived and is no longer actively developed.

Example DIDs:
- `did:elem:ropsten:EiBOWH8368BI9NSaVZTmtxuqwpfN9NwAwy4Z95_VCl6A9g`
- `did:elem:mainnet:EiBOWH8368BI9NSaVZTmtxuqwpfN9NwAwy4Z95_VCl6A9g`

### DID Structure

- **Prefix**: `did:elem:`
- **Network** (optional): `ropsten`, `mainnet` (default: ropsten)
- **Unique suffix**: SHA256 hash of encoded create payload

### Architecture

- **Sidetree protocol**: Layer 2 DID anchoring
- **Ethereum**: Ledger layer for anchoring
- **IPFS**: Content-addressable storage for operations

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Submit DID document model as payload |
| Read | Resolve through Element node's Sidetree API |
| Update | Requires previous operation hash + patches |
| Delete | Minimal payload with DID unique suffix |

### Supported Patches

- Add/remove public keys
- Add/remove authentication methods
- Add/remove assertion methods
- Add/remove capability delegation/invocation
- Add/remove key agreements
- Add/remove service endpoints

## Evaluation

### 1. Feasibility/Complexity

**Not applicable - Project archived**

While the Sidetree architecture is interesting:

- **Ethereum dependency**: Requires Ethereum for anchoring
- **IPFS dependency**: Requires IPFS for content storage
- **Archived project**: No longer maintained

**Adaptation possibility**: N/A due to archived status.

### 2. Ecosystem

**Archived / Historical**

- **DIF project**: Was a major DIF initiative
- **Sidetree pioneer**: Early Sidetree implementation
- **No longer maintained**: Read-only repository
- **Superseded**: By other Sidetree implementations

### 3. Stability

**Archived**

- **February 2021**: Project archived
- **No updates**: Read-only status
- **Historical reference**: Useful for understanding Sidetree

## Special Considerations

### Sidetree Relevance

Element demonstrated Sidetree's ledger-agnostic approach, which is relevant for did:ion investigation. The patterns for:

- Batch anchoring
- Content-addressed operations
- Update patches

Are still valuable for understanding did:ion and similar methods.

## Recommendation

**No-go**

The did:elem method should not be used:

1. **Archived project**: No longer maintained since 2021
2. **Ethereum/IPFS dependency**: External infrastructure required
3. **Superseded**: did:ion and other Sidetree implementations are active
4. **No support**: Cannot rely on unmaintained software

For Sidetree-based DIDs, investigate did:ion instead.
