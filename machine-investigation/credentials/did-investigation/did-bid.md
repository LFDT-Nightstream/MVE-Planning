# did:bid (BIF Blockchain DID Method)

| Property | Value |
|----------|-------|
| Specification | [BID Protocol Spec](https://github.com/teleinfo-bif/bid/blob/master/doc/en/BID%20Protocol%20Specification.md) |
| Organization | Teleinfo/BIF |
| DID Format | `did:bid:[acsn:]<suffix>` |

## Overview

The did:bid method is the native DID system for the BIF (Blockchain-based Identifier Framework) network, a Chinese blockchain infrastructure project. It supports multiple cryptographic algorithms and features a sidechain architecture.

Example DID: `did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG`

### DID Structure

- **Prefix**: `did:bid:`
- **acsn** (optional): 4-character access chain code for sidechains
- **suffix**: 22-42 alphanumeric characters (Base58/Base64/Bech32 encoded)

Address encoding prefixes:
- `f` = Base58
- `s` = Base64
- `t` = Bech32

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | HTTP POST with proof signature |
| Read | HTTP GET returns JSON-LD document |
| Update | HTTP request (authentication fields immutable) |
| Deactivate | Disables BID functionality |
| Recovery | Restore control using recovery keys |

### Supported Algorithms

- **SM2** (designated 'z') - Chinese national standard
- **ED25519** (designated 'e')
- **Secp256k1** (designated 's')

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bid method is tied to the BIF blockchain:

- **BIF blockchain required**: DIDs are the "native address of BIF-core"
- **Sidechain architecture**: Complex multi-chain system with access chain codes
- **Chinese standards**: Includes SM2 (Chinese national cryptographic standard)
- **Recovery mechanism**: Interesting but requires BIF blockchain

**Adaptation possibility**: Very low. The method is deeply integrated with BIF's specific blockchain architecture and consensus mechanism (DPOS + PBFT).

### 2. Ecosystem

**Regional / Chinese market**

- **Chinese origin**: Developed for Chinese blockchain infrastructure
- **SM2 support**: Chinese national cryptographic standard
- **Limited international adoption**: Primarily used in Chinese projects
- **BIF-specific**: Tied to specific blockchain ecosystem

### 3. Stability

**Mature within its ecosystem**

- **Comprehensive specification**: Well-documented protocol
- **Multiple algorithms**: Supports diverse cryptographic options
- **Recovery feature**: Robust key recovery mechanism
- **TTL caching**: Supports resolution caching

## Special Considerations

- **Recovery keys**: Can restore control if authentication keys compromised
- **Sidechain support**: Access chain codes for multi-chain resolution
- **SM2 algorithm**: Chinese government cryptographic standard
- **Privacy design**: Only encrypted hashes stored on-chain

## Recommendation

**No-go**

The did:bid method is unsuitable for our use case:

1. **BIF blockchain dependency**: Requires specific blockchain infrastructure
2. **Regional focus**: Primarily serves Chinese market with SM2 support
3. **Complex architecture**: Sidechain system adds unnecessary complexity
4. **Limited international adoption**: Not widely used outside China

The recovery key mechanism is interesting for enterprise use cases but doesn't justify the integration complexity.
