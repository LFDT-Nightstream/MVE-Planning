# did:ccp (Baidu Cloud DID Method)

| Property | Value |
|----------|-------|
| Specification | [Baidu DID Spec](https://did.baidu.com/did-spec/) |
| Organization | Baidu |
| DID Format | `did:ccp:<method-specific-id>` |

## Overview

The did:ccp method is Baidu's decentralized identity system, built on consortium/alliance blockchain infrastructure. It follows W3C standards and uses a Bitcoin-style double-hashing approach for identifier generation.

Example DID: `did:ccp:1FsbKR6UpV6GW8o8szccdxXkquzTg2VZLL`

### DID Generation

The method-specific ID is generated via:
```
base58(ripemd160(sha256(<Base DID Document>)))
```

### Key Structure

- **Primary key pair**: For authentication
- **Recovery key pair**: For backup/recovery operations
- **Algorithm**: Secp256k1 (RSA marked as TBD)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate key pairs → create Base Document → hash → register |
| Read | DID Resolver returns document via REST API |
| Update | Requires recovery key signature |
| Delete | Revocation via signed request using recovery key |

### Ecosystem Components

- DID Mini-program
- Command-line tools
- Online resolution service (https://did.baidu.com)
- JavaScript and Golang SDKs
- Identity Hub with API
- Issuer registry system

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ccp method requires:

- **Baidu blockchain infrastructure**: Built on consortium blockchain (Quorum-based)
- **Off-chain storage**: Privacy-sensitive data stored at issuing authorities
- **Centralized resolution**: Resolves through Baidu's DID service

**Adaptation possibility**: Low. While the identifier generation is straightforward (hash-based), the entire verification and resolution system depends on Baidu's infrastructure.

### 2. Ecosystem

**Regional / Single-vendor**

- **Baidu-controlled**: Single vendor maintains all infrastructure
- **Chinese market focus**: Primarily targets Chinese use cases
- **Good tooling**: SDKs available in multiple languages
- **Use cases**: Digital identity, KYC, IoT, smart cities

### 3. Stability

**Stable but vendor-locked**

- **Active service**: Baidu maintains the infrastructure
- **Production use**: Deployed for real use cases
- **Vendor risk**: Entirely dependent on Baidu's continued support

## Special Considerations

- **Recovery mechanism**: Two-key system (primary + recovery) is a good design pattern
- **Privacy approach**: Off-chain storage for sensitive data
- **Consortium model**: Not truly decentralized

## Recommendation

**No-go**

The did:ccp method is unsuitable for our use case:

1. **Baidu dependency**: Requires Baidu's blockchain and resolution infrastructure
2. **Regional focus**: Designed for Chinese market and regulations
3. **Vendor lock-in**: No path to self-hosted or decentralized operation
4. **Consortium blockchain**: Cannot verify without access to Baidu's network

The two-key (primary + recovery) pattern is worth noting for our own design, but the implementation is too vendor-specific.
