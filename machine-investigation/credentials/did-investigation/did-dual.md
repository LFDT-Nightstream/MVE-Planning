# did:dual (Dual DID Method)

| Property | Value |
|----------|-------|
| Specification | [Dual DID Spec](https://github.com/Smart-ID-Card/Dual-DID/blob/main/docs/dual-did-method.md) |
| Organization | Smart-ID-Card |
| DID Format | `did:dual:<chain-id>:<ethereum-address>` |

## Overview

The did:dual method is a decentralized identifier based on Ethereum addresses, supporting multiple Ethereum-compatible networks via chain ID.

Example DID: `did:dual:0x420ada7:123456789abcdef123456789abcdef123456789abc`

### DID Structure

- **Prefix**: `did:dual:`
- **Network identifier**: Hex-encoded chain ID
- **Address**: Ethereum address (secp256k1 derived)

### Key Features

- **Multi-network**: Supports multiple EVM chains via chain ID
- **No on-chain registration**: Identity created from key derivation
- **Limited operations**: Only create and delete supported

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate Ethereum address from secp256k1 key |
| Read | Only deletion status verifiable from blockchain |
| Update | **Not supported** |
| Delete | Via Dual DID API |

### Supported Algorithms

- secp256k1 (Ethereum standard)

## Evaluation

### 1. Feasibility/Complexity

**Partially feasible but limited**

The did:dual method is similar to did:ethr in concept:

- **Key derivation**: Standard secp256k1, works offline
- **No registration required**: DID exists from key generation
- **Blockchain for deletion**: Only deletion state on-chain
- **EVM dependency**: Deletion check requires chain access

**Adaptation possibility**: The key derivation works offline, but we cannot verify deletion status without EVM chain access.

### 2. Ecosystem

**Limited**

- **Smart-ID-Card**: Unknown organization
- **Minimal documentation**: Basic specification
- **EVM-based**: Part of broader Ethereum ecosystem
- **Limited features**: No updates, limited operations

### 3. Stability

**Early stage**

- **W3C CCG compliant**: Follows specification
- **Limited activity**: Small repository
- **Simple design**: Fewer moving parts

## Special Considerations

### Security Notes

- Private keys should be stored in SE/HSM
- No PII in DID documents or blockchain
- Only Verifiable Credentials contain personal data

### Similarities to did:ethr

- Ethereum address-based
- secp256k1 cryptography
- Multi-chain via chain ID

## Recommendation

**No-go**

The did:dual method is unsuitable for our use case:

1. **EVM dependency**: Deletion verification requires chain access
2. **Limited features**: No updates supported
3. **Similar to did:ethr**: Same fundamental limitations
4. **Unknown ecosystem**: Limited adoption and documentation

This is essentially a simpler variant of did:ethr with fewer features but the same chain dependency issues.
