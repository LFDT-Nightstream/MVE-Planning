# did:cheqd (cheqd DID Method)

| Property | Value |
|----------|-------|
| Specification | [cheqd DID Method ADR](https://docs.cheqd.io/identity/architecture/adr-list/adr-001-cheqd-did-method) |
| Organization | cheqd |
| DID Format | `did:cheqd:[namespace]:[unique-id]` |

## Overview

The did:cheqd method is built on the Cosmos blockchain framework, designed as a W3C-compliant alternative to Hyperledger Indy for self-sovereign identity (SSI). It supports both UUID-style and Indy-compatible identifier formats.

Example DIDs:
- UUID-style: `did:cheqd:mainnet:de9786cd-ec53-458c-857c-9342cf264f80`
- Indy-style: `did:cheqd:mainnet:TAwT8WVt3dz2DBAifwuSkn`

### Identifier Styles

| Style | Format | Generation |
|-------|--------|------------|
| UUID | Standard UUID v4 | Any application can generate |
| Indy | 16-22 char Base58 | First 16 bytes of SHA256(initial public key) |

### Architecture

- **Cosmos SDK**: Built on Cosmos blockchain framework
- **Tendermint BFT**: Byzantine Fault Tolerant consensus
- **Identity-layer separation**: DID keys separate from Cosmos node-layer keys
- **Privacy protection**: Controllers don't need on-ledger accounts

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | `CreateDidDocRequest` with DIDDoc + controller signatures |
| Read | `GetDidDoc` query via Tendermint RPC |
| Update | `UpdateDidDocRequest` with previous versionId for replay protection |
| Deactivate | `DeactivateDidDocRequest` - permanent, cannot be reactivated |

### Supported Key Formats

- Ed25519 (primary)
- JWK (RFC 7517)
- Multibase
- Base58

## Evaluation

### 1. Feasibility/Complexity

**Requires investigation**

The did:cheqd method is interesting because:

- **Cosmos-based**: Uses Cosmos SDK which has well-documented patterns
- **Indy compatibility**: Designed to work with existing Aries-based tools
- **Separation of concerns**: Identity layer separate from consensus layer

**Potential adaptation**: Moderate. The core DIDDoc operations (create, update, deactivate) could potentially be adapted to our ledger. The specification is well-documented and follows W3C standards closely.

**Key challenge**: Would require implementing Cosmos-style transaction handling or adapting the protocol.

### 2. Ecosystem

**Growing SSI ecosystem**

- **Hyperledger Aries compatible**: Works with existing SSI libraries
- **W3C compliant**: Full DID Core specification support
- **Active development**: Implementation status: Implemented
- **Migration path**: Designed for Indy → cheqd migration

### 3. Stability

**Stable and maintained**

- **ADR Stage**: ACCEPTED
- **Implementation**: Complete
- **Last Updated**: February 6, 2023
- **Corporate backing**: cheqd maintains infrastructure

## Special Considerations

### Why Not Hyperledger Indy?

cheqd identifies three Indy limitations:
1. Permissioned ledger - limited write access
2. ~25 node limit due to Plenum BFT throughput
3. No sophisticated token implementation

### Design Patterns Worth Noting

- **Version tracking**: `versionId`, `previousVersionId`, `nextVersionId`
- **Replay protection**: Previous version required for updates
- **Metadata**: Created/updated timestamps, deactivation status
- **Linked resources**: Privacy-respecting resource attachments

## Recommendation

**Requires investigation**

The did:cheqd method has potential but needs further analysis:

1. **Interesting patterns**: Version tracking, replay protection, metadata handling
2. **Well-documented**: Clear separation of identity and consensus layers
3. **Cosmos SDK**: Could inform our own implementation approach

**Investigation needed**:
- Can the DIDDoc operations be adapted independently of Cosmos?
- What would a minimal implementation look like?
- Is Indy compatibility valuable for our use case?

The design patterns are solid and worth studying, even if we don't adopt the method directly.
