# did:art (Artwork ID DID Method)

| Property | Value |
|----------|-------|
| Specification | [ArtracID DID ART Method](https://github.com/ArtracID/ArtracID-DID-ART-Method) |
| Organization | ArtracX / Art Group Limited |
| DID Format | `did:art:<blockchain-id>:<subject-id>` |

## Overview

The did:art method is a decentralized identifier system designed specifically for artwork identification. It uses the Enecuum (ENQ) blockchain for storage and a centralized resolver service.

Example DID: `did:art:enq:f045c5c7d50145b65ca2702c38b4e2d46658293c`

### DID Structure

- **Prefix**: `did:art:`
- **Blockchain ID**: 1-5 characters (a-z)
- **Subject ID**: 40 hexadecimal characters (derived from hashed public key)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | POST to `/document/{did}` with operation "create" |
| Read | GET from `https://did.artrac.id/{subject-id}` |
| Update | PUT to `/document/{did}` with operation "update" |
| Delete | DELETE to resolver endpoint |

### Supported Algorithms

- EcdsaSecp256k1VerificationKey2019 (secp256k1)
- Hardware security devices for key storage

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:art method has several limitations:

- **Centralized resolver**: Resolution goes through `did.artrac.id` - a single centralized service
- **Blockchain dependency**: Uses Enecuum (ENQ) blockchain for storage
- **Domain-specific**: Designed specifically for artwork identification
- **REST API based**: All CRUD operations via HTTP endpoints to centralized service

**Adaptation possibility**: Very low. The method is tightly coupled to both the Enecuum blockchain and a centralized resolver service. There's no way to verify DIDs without trusting these external systems.

### 2. Ecosystem

**Very limited / Niche**

- **Single purpose**: Designed specifically for artwork/art market use cases
- **Small organization**: Maintained by ArtracX / Art Group Limited
- **Limited adoption**: No evidence of widespread use beyond the art industry
- **No Universal Resolver support**: Not integrated with DIF Universal Resolver

### 3. Stability

**Early / Uncertain**

- **Version 1.0**: First version dated May 2022
- **Recently registered**: W3C registry merge December 2022
- **Single maintainer**: Organization-specific development
- **Limited documentation**: Minimal specification details

## Special Considerations

- **Hardware key storage**: Requires hardware security devices for private keys
- **Art industry focus**: Specifically designed for artwork provenance and identification
- **Centralized trust**: Despite using blockchain, relies on centralized resolver

## Recommendation

**No-go**

The did:art method is unsuitable for our use case:

1. **Centralized resolution**: Requires trusting a single resolver service (`did.artrac.id`)
2. **Domain-specific**: Designed for art industry, not general-purpose identity
3. **Blockchain dependency**: Requires Enecuum blockchain access
4. **Limited ecosystem**: Very niche with minimal adoption

This method solves a specific problem for the art market but doesn't provide any features useful for a general-purpose DID registry.
