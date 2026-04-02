# did:ctid (CTID Digital Identity Chain)

| Property | Value |
|----------|-------|
| Specification | [CTID DID Spec](https://github.com/617640999/ctid-did-register/blob/main/anicert-ctid-did-en.md) |
| Organization | Beijing Zhongdun Anxin Science and Technology Development Co., Ltd. |
| DID Format | `did:ctid:<64-char-hex-msi>` |

## Overview

CTID (Digital Identity Chain) provides distributed digital identity authentication services across various industries in China. It uses SM3/SM2 (Chinese national cryptographic standards) for security.

Example DID: `did:ctid:16D44117484372A2D010BDAA56703E723FB4C5C06CA44105F0C9C5B8020A7073`

### DID Structure

- **Prefix**: `did:ctid:` (lowercase required)
- **MSI**: 64 hexadecimal characters from SM3 hash of personal identity attributes

### Generation Process

1. Hash personal identity attributes using SM3 algorithm
2. Convert to uppercase hexadecimal (64 characters)
3. Personal information cannot be reverse-associated with DID

### CRUD Operations

| Operation | Endpoint | Method |
|-----------|----------|--------|
| Create | `/didservice/v1/did/create` | POST |
| Read | `/didservice/v1/did/get` | POST |
| Update | `/didservice/v1/did/update` | POST |
| Delete | `/didservice/v1/did/del` | POST |

### Cryptographic Standards

- **Hashing**: SM3 (Chinese national standard)
- **Signatures**: SM2 (sm2p256v1 elliptic curve)
- **Key storage**: Device TEE or secure keystore

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ctid method requires:

- **CTID Digital Identity Chain**: Proprietary blockchain ledger
- **SM2/SM3 cryptography**: Chinese national standards (limited global support)
- **SDK access**: Qualified institutions receive certificates for access
- **Identity attributes**: DIDs derived from personal identity data

**Adaptation possibility**: Very low. The system requires Chinese national cryptographic standards and institutional certification for SDK access.

### 2. Ecosystem

**Regional / Government-backed**

- **Chinese company**: Beijing Zhongdun Anxin
- **Government standards**: Uses Chinese national cryptography
- **Institutional access**: Only qualified organizations can access
- **Multi-industry**: Various industry applications

### 3. Stability

**Enterprise stable**

- **Version**: 1.0.0
- **Editor's Draft**: W3C CCG status
- **Production use**: Deployed for real applications
- **SDK available**: Self-developed CTID SDK

## Special Considerations

### Security Features

- Environment security detection (blocks simulators/rooted devices)
- Multi-layer data encryption
- White-box algorithm implementation
- Virtual machine instruction protection
- TEE-based key storage

### Limitations

- **SM2/SM3 only**: Not globally interoperable
- **Institutional access**: Not open to all developers
- **Personal data derivation**: Privacy concerns with identity-based hashing

## Recommendation

**No-go**

The did:ctid method is unsuitable for our use case:

1. **Chinese cryptography**: SM2/SM3 have limited global support
2. **Institutional access**: Requires certification for SDK access
3. **Personal data**: DIDs derived from identity attributes
4. **Proprietary platform**: CTID Digital Identity Chain required

This is essentially a government-backed identity system for Chinese institutions, not a general-purpose DID method.
