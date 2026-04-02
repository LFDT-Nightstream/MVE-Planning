# did:cndid (China Decentralized Identifier)

| Property | Value |
|----------|-------|
| Specification | [CNDID Protocol Spec](https://github.com/teleinfo-bif/cndid/blob/main/doc/en/CNDID%20Protocol%20Specification.md) |
| Organization | teleinfo-bif |
| DID Format | `did:cndid:[ACSN:]<22-42-char-identifier>` |

## Overview

CNDID provides decentralized identity identification services for individuals, enterprises, devices, and digital objects based on blockchain technology. It supports multiple encryption algorithms and cross-chain identity resolution.

Example DID: `did:cndid:sf24eYrmwXt6nx4fig3XJm7n9UP6PNRJ3`

### DID Structure

- **Prefix**: `did:cndid:`
- **ACSN** (optional): 4-character namespace for sidechain networks
- **Identifier**: 22-42 alphanumeric characters

### Generation Methods

| Algorithm | Description |
|-----------|-------------|
| SM2 | Chinese national cryptographic standard |
| ED25519 | EdDSA with Curve25519 |
| Secp256k1 | ECDSA standard curve |

Identifier = Base58/Base64/Base32 encoding of public key

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | POST request with DID document and proof signature |
| Read | GET request retrieves document and metadata |
| Update | Modify document properties with proof integrity |
| Deactivate | Mark DID as inactive |
| Recover | Restore control using recovery keys |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:cndid method requires:

- **Blockchain network**: Requires specific blockchain infrastructure
- **Cross-chain support**: Designed for sidechain network integration
- **Chinese standards**: Uses SM2 cryptography (Chinese national standard)
- **Recovery mechanism**: Built-in key recovery system

**Adaptation possibility**: Low. While the identifier generation is straightforward, the system depends on specific blockchain infrastructure and cross-chain resolution mechanisms.

### 2. Ecosystem

**Regional / Chinese focus**

- **teleinfo-bif**: Chinese blockchain infrastructure organization
- **Chinese cryptography**: SM2 support suggests Chinese market focus
- **Cross-chain features**: Designed for multi-chain environments
- **Limited visibility**: Minimal international adoption

### 3. Stability

**Active development**

- **Version**: 1.0.0
- **Specification complete**: Well-documented protocol
- **Blockchain-based**: Production deployment mentioned

## Special Considerations

### Interesting Features

- **Recovery keys**: Built-in recovery mechanism for key compromise
- **Cross-chain resolution**: DIDSubResolver for sidechain networks
- **Multiple encodings**: Base58, Base64, Base32 support
- **SM2 support**: Chinese national cryptographic standard

### Limitations

- **Infrastructure dependency**: Requires specific blockchain network
- **Chinese focus**: SM2 cryptography limits international interoperability

## Recommendation

**No-go**

The did:cndid method is unsuitable for our use case:

1. **Blockchain dependency**: Requires specific blockchain infrastructure
2. **Regional focus**: Designed for Chinese market with SM2 support
3. **Cross-chain complexity**: Sidechain resolution adds complexity
4. **Infrastructure requirements**: Cannot self-host without their network

The recovery key mechanism is a good pattern to note, but the overall system is too infrastructure-dependent.
