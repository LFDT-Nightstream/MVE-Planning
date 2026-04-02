# did:bryk (Bryk DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:bryk Spec](https://github.com/aidtechnology/did-method) |
| Organization | Bryk.io / AID Technology |
| DID Format | `did:bryk:[tag:]<id-string>` |

## Overview

The did:bryk method is a flexible DID system that can work with various storage backends. It features a proof-of-work ticket mechanism for publishing and cryptographic proofs for document integrity. The specification emphasizes being "independent of data storage and message delivery mechanisms."

Example DID: `did:bryk:c137:02825c9d-6660-4f17-92db-2bd22c4ed902`

### DID Structure

- **Prefix**: `did:bryk:`
- **Tag** (optional): Namespace/classification mechanism
- **ID String**: Either UUID v4 or 32-byte SHA3-256 hash (64 hex chars)

### ID String Modes

1. **Mode UUID**: RFC4122 UUID v4 (`time-low-time-mid-time-high-and-version-...`)
2. **Mode Hash**: SHA3-256 hash encoded as 64 lowercase hex characters

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Submit request ticket with proof-of-work and signature |
| Read | HTTP GET to `did.bryk.io/v1/retrieve/{method}/{subject}` |
| Update | Authenticated ticket submission with validation |
| Delete | Not explicitly documented |

### Supported Algorithms

- Ed25519 (Ed25519VerificationKey2018)
- RSA (minimum 4096 bits, RSASignature2018)
- secp256k1 (EdDSA signature variant)

## Evaluation

### 1. Feasibility/Complexity

**Partially interesting but not directly applicable**

The did:bryk method has some appealing characteristics:

- **Storage agnostic**: Designed to work with various backends (including DLT)
- **Proof-of-work**: Anti-spam mechanism for publishing
- **Cryptographic proofs**: Document integrity verification
- **Flexible tagging**: Namespace mechanism for classification

However:
- **Centralized default**: Default resolution through `did.bryk.io`
- **Custom protocol**: Ticket mechanism requires specific implementation
- **gRPC/protobuf**: Reference implementation uses specific technologies

**Adaptation possibility**: Moderate conceptually. The storage-agnostic design philosophy aligns with our needs, but the specific implementation (proof-of-work tickets, gRPC) would need adaptation. The cryptographic proof concept for document integrity is valuable.

### 2. Ecosystem

**Limited**

- **Single organization**: Bryk.io / AID Technology
- **Reference implementation**: Go-based CLI and agent available
- **Limited adoption**: Not widely used outside bryk ecosystem
- **No Universal Resolver driver**: Custom resolution only

### 3. Stability

**Stable but niche**

- **Well-documented**: Comprehensive specification
- **Reference implementation**: Working code available
- **Active development**: Go packages maintained
- **Single maintainer risk**: Organization-specific

## Special Considerations

- **Cryptographic proofs**: All mutations include integrity proofs (even though removed from DID Core, bryk keeps them)
- **Proof-of-work**: Novel anti-spam approach
- **Tag system**: Flexible namespace classification
- **Storage agnostic design**: Philosophy aligns with our goals

## Recommendation

**No-go (but informative)**

The did:bryk method isn't directly usable but has interesting concepts:

1. **Default centralization**: Resolution defaults to bryk.io infrastructure
2. **Custom protocols**: Would need to implement their ticket/PoW system
3. **Limited ecosystem**: Single-vendor solution

**Worth noting:**
- The storage-agnostic philosophy is valuable
- Cryptographic proofs on documents provide integrity verification
- The flexible tagging/namespace system could inform our design
- Proof-of-work for rate limiting is an interesting anti-spam mechanism

These concepts could inform our own DID method design without adopting the bryk-specific implementation.
