# did:content (Content DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:content Spec](https://github.com/KataruInc/did-content-spec) |
| Organization | KataruInc |
| DID Format | `did:content:<Base58(RIPEMD160(SHA3-256(DID Document)))>` |

## Overview

The did:content method creates decentralized identifiers for digital content (images, video, music) that resolve across multiple storage providers rather than depending on centralized platforms.

Example DID: `did:content:3qrgME9eV7brjYsx3Ebbp9hubiQna1wcD1V6xmZwSdLXzCG`

### DID Structure

- **Prefix**: `did:content:`
- **Identifier**: Base58(RIPEMD160(SHA3-256(DID Document without id)))

### Key Components

- **DID Resolver**: Generates DIDs, creates documents, maintains location database
- **Verifiable Data Registry (VDR)**: Stores documents on distributed systems (IPFS, S3)
- **DID Document**: Contains controller info, verification methods, service endpoints

### CRUD Operations

| Operation | Endpoint | Method |
|-----------|----------|--------|
| Create | `/create?contentType=&contentUrl=&signature=` | POST |
| Read | `/resolve?did=?` | GET |
| Update | `/update?did=&contentType=&contentUrl=&signature=` | POST |
| Delete | `/delete?did=` | POST |

### Supported Algorithms

- ECDSA Secp256k1

## Evaluation

### 1. Feasibility/Complexity

**Not directly applicable**

The did:content method is designed for a different purpose:

- **Content identification**: Identifies digital content, not entities
- **Centralized resolver**: Requires DID Resolver service to maintain mappings
- **Storage agnostic**: Works with IPFS, S3, or other storage
- **No blockchain required**: Uses traditional database for mappings

**Key distinction**: This is about identifying *content*, not *controllers/owners*. The DID points to where content can be found, not who owns it.

**Adaptation possibility**: N/A. Different use case than what we're building.

### 2. Ecosystem

**Early stage**

- **Alpha status**: Released July 28, 2023
- **Minimal adoption**: 2 GitHub stars
- **W3C registered**: Officially listed but experimental
- **Open source**: Resolver available for self-hosting

### 3. Stability

**Experimental**

- **Development stage**: Alpha
- **KataruInc**: Single organization maintains
- **Early adoption**: Limited external integration

## Special Considerations

### Unique Features

- **Content-centric**: DID identifies content, not identity
- **Royalty tracking**: Includes royalty recipient percentages
- **Fragment support**: `#author`, `#rights-holder` for role-specific resolution
- **Storage flexible**: Not tied to specific storage provider

### Limitations

- **Centralized mapping**: Database maps DID to storage location
- **Not identity-focused**: Different purpose than typical DID methods
- **Early stage**: Alpha quality

## Recommendation

**No-go**

The did:content method is unsuitable for our use case:

1. **Different purpose**: Designed for content identification, not entity identity
2. **Centralized resolver**: Depends on resolver service maintaining mappings
3. **Early stage**: Alpha quality with minimal adoption
4. **Not applicable**: Doesn't solve our identity/authentication needs

This method solves a different problem (content location) than what we need (entity identity for contract management).
