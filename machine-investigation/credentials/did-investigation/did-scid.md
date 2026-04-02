# did:scid (StraitsChain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/StraitsChain/SCID/blob/main/README.md) |
| Organization | StraitsChain |
| DID Format | `did:scid:<base58-encoded-id>` |

## Overview
did:scid is a DID method for the StraitsChain blockchain platform. The identifier is composed of version, type, and StraitsChain address, all base58-encoded. Designed for trusted digital identity and data exchange services.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires StraitsChain blockchain:

- Based on blockchain for identity and data exchange
- Resolution by DID identifier or search index
- Uses Secp256k1 signature algorithms
- Smart contracts for DID operations
- Supports agent delegation for platform operations

User data maintained locally, but identity operations require blockchain.

### 2. Ecosystem
**Very small.** Appears to be a specific platform project. Limited documentation and adoption evidence.

### 3. Stability
**Early stage.** Specification marked as draft pending W3C alignment. Active development indicated.

## Recommendation
**No-go**

Requires StraitsChain blockchain infrastructure for identity operations. Not suitable for self-contained use.
