# did:ct (CircularTrust DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:ct Spec](https://github.com/blueroominnovation/did-ct-spec) |
| Organization | BlueRoom Innovation |
| DID Format | `did:ct:<iden3-genesis-id>` |

## Overview

The did:ct method is designed for CircularTrust, a Hyperledger Besu-based blockchain platform for circular economy information sharing. It uses IDEN3 for identity management and supports zero-knowledge proofs.

Example DID: `did:ct:zuerR5X7JKmBM6CqtLmU6a8XWJhEaW1WDe2tZapCg`

### DID Structure

- **Prefix**: `did:ct`
- **Identifier**: IDEN3 Genesis ID

### Key Features

- **IDEN3 integration**: Uses IDEN3 identity framework
- **Merkle tree proofs**: Validity and revocation verification
- **Zero-knowledge proofs**: Privacy-preserving credential presentation
- **Hyperledger Besu**: Enterprise Ethereum client

### CRUD Operations

All operations require API keys for authorized holders:

| Operation | Endpoint |
|-----------|----------|
| Create | `/api/v1/did/identity/createDID` |
| Read | `/api/v1/did/identity/resolveDid` |
| Update | `/api/v1/did/identity/updateDID` |
| Delete | `/api/v1/did/identity/deleteDID` |

(All at `identityhub.blueroominnovation.com`)

### Security Features

- Digital signatures (controller-only credential issuance)
- Merkle tree proofs
- Zero-knowledge proofs for credential presentation
- Private key safeguarding

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ct method requires:

- **CircularTrust infrastructure**: Specific blockchain platform
- **IDEN3 framework**: Complex identity management system
- **Hyperledger Besu**: Enterprise Ethereum variant
- **API key access**: Authorized holder requirement

**Adaptation possibility**: Very low. The system is tightly coupled to CircularTrust's infrastructure and IDEN3's zero-knowledge proof system.

### 2. Ecosystem

**Niche / Single platform**

- **BlueRoom Innovation**: Single company
- **Circular economy focus**: Specific industry vertical
- **Early stage**: 2 commits, no releases
- **IDEN3**: Interesting ZK framework but adds complexity

### 3. Stability

**Early stage**

- **Minimal activity**: Very limited GitHub activity
- **Internal development**: Appears to be internal project
- **Enterprise focus**: Circular economy use case

## Special Considerations

### Interesting Features

- **Zero-knowledge proofs**: Privacy-preserving credential verification
- **IDEN3**: Advanced identity framework with ZK capabilities
- **Merkle trees**: Efficient revocation checking

### Limitations

- **Platform lock-in**: Only works on CircularTrust
- **API key requirement**: Not permissionless
- **Complex stack**: IDEN3 + Besu + CircularTrust

## Recommendation

**No-go**

The did:ct method is unsuitable for our use case:

1. **CircularTrust dependency**: Requires their specific platform
2. **IDEN3 complexity**: Adds ZK infrastructure overhead
3. **API key access**: Not self-sovereign in practice
4. **Limited ecosystem**: Minimal adoption and documentation

The zero-knowledge proof approach is interesting for privacy but the platform lock-in makes it impractical.
