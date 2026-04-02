# did:cr (Copyright DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:cr Spec](https://github.com/SelfSovereignDrm/core/blob/main/README.md) |
| Organization | SelfSovereignDrm |
| DID Format | `did:cr:<type>:<uuid>` |

## Overview

The did:cr method identifies copyright works and copyright holders. It defines two types of DIDs: CRH (Copyright Holder) and CRW (Copyright Work), using UUID-based identifiers.

Example DIDs:
- Copyright holder: `did:cr:h:809a1169-a990-40dc-a325-b95d03e5acd7`
- Copyright work: `did:cr:w:4f75568d-6b09-4b07-ab99-7c9114b733b4`

### DID Structure

- **Type**: `h` (copyright holder) or `w` (copyright work)
- **Identifier**: UUID per RFC4122

### Document Types

| Type | Purpose |
|------|---------|
| CRH DID | Identifies copyright holder (controller) |
| CRW DID | Identifies copyrighted work (asset) |

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Holders generate their own CRH DID; registration optional |
| Read | Follows W3C DID resolution specs |
| Update | Controllers can update; recommend new DID over target modification |
| Deactivate | Deactivated CRH cannot control active CRW |

### Service Endpoints

- **RightOffer Service**: Publishes licensing rules using W3C ODRL
- **WorkMetadata Service**: References external metadata (Dublin Core, etc.)

## Evaluation

### 1. Feasibility/Complexity

**Partially feasible but different use case**

The did:cr method is architecture-agnostic:

- **No blockchain required**: Uses standard DID infrastructure
- **UUID-based**: Simple identifier generation
- **Self-issued**: Holders create their own DIDs

**Key distinction**: This is about copyright/rights management, not identity verification. CRW DIDs identify works, CRH DIDs identify rights holders.

**Adaptation possibility**: High for the format, but N/A for our use case - this solves a different problem.

### 2. Ecosystem

**Niche / Rights management**

- **Draft status**: Subject to change
- **ODRL integration**: Uses W3C rights expression language
- **Copyright focus**: Specific to creative works
- **Limited adoption**: No active registry mentioned

### 3. Stability

**Draft**

- **Document status**: Draft, unstable
- **SelfSovereignDrm**: Single organization
- **Standards-based**: Uses W3C specs (DID, ODRL)

## Special Considerations

### Interesting Features

- **Dual DID types**: Separate DIDs for holders vs works
- **ODRL integration**: Standardized rights expression
- **HASHLINK**: Integrity protection for service endpoints
- **alsoKnownAs**: Links to ISBNs, DOIs, etc.

### Use Case Mismatch

- Designed for copyright management
- CRW DIDs identify assets, not controllers
- Rights licensing focus, not authentication

## Recommendation

**No-go**

The did:cr method is unsuitable for our use case:

1. **Different purpose**: Designed for copyright/rights management
2. **Asset identification**: CRW DIDs identify works, not entities
3. **Draft status**: Specification not stable
4. **Use case mismatch**: Solves licensing, not authentication

The dual DID type (holder/work) pattern could inform asset management, but our focus is on identity for contract management, not copyright.
