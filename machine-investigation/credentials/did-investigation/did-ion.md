# did:ion (Identity Overlay Network DID Method)

| Property | Value |
|----------|-------|
| Specification | [Sidetree Spec](https://identity.foundation/sidetree/spec/) / [ION](https://github.com/decentralized-identity/ion) |
| Organization | DIF / Microsoft |
| DID Format | `did:ion:<sidetree-id>` |

## Overview

The did:ion method implements the Sidetree protocol on top of Bitcoin, enabling scalable decentralized identifiers. It batches thousands of DID operations into single Bitcoin transactions, dramatically reducing costs while maintaining Bitcoin's security guarantees.

Example DID: `did:ion:EiAnKD8-jfdd0MDcZUjAbRgaThBrMxPTFOxcnfJhI7Ukaw`

### Sidetree Protocol

Sidetree is a Layer 2 protocol that:
- Batches up to 10,000 operations per anchor transaction
- Uses IPFS for content-addressable storage of operation data
- Provides deterministic resolution without special consensus

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate self-certifying DID with initial PKI metadata |
| Update | Delta-based modification of keys/services |
| Recover | Replace all cryptographic material using recovery key |
| Deactivate | Permanently remove DID from system |

### Architecture

- **Anchor Layer**: Bitcoin provides chronological ordering and immutability
- **CAS Layer**: IPFS stores operation files (index files, chunk files)
- **Resolution**: Deterministic processing of operation history

## Evaluation

### 1. Feasibility/Complexity

**Partially feasible - Ledger-agnostic design is interesting**

The Sidetree protocol has interesting properties:

**Positive:**
- **Ledger-agnostic**: Designed to work with any anchoring system
- **Scalable batching**: Thousands of operations per transaction
- **CRDT-based**: Conflict-free replicated data types
- **Key rotation**: Full update/recovery support

**Challenges for our use case:**
- **Dual-layer complexity**: Requires both anchor layer and CAS layer
- **IPFS dependency**: Default implementation uses IPFS for storage
- **Full history needed**: Resolution requires processing entire operation chain
- **Bitcoin verification**: Default ION requires Bitcoin state access

**Adaptation possibility**: Moderate to High. The Sidetree protocol is explicitly ledger-agnostic. We could potentially:
1. Use our ledger as the anchor layer instead of Bitcoin
2. Provide our own CAS implementation or use IPFS
3. Implement Sidetree resolution logic

This would give us a DID method with full key rotation, recovery, and enterprise features.

### 2. Ecosystem

**Strong / Microsoft-backed**

- **Microsoft development**: Major corporate backing
- **DIF incubation**: Decentralized Identity Foundation standard
- **Production use**: Live on Bitcoin mainnet since 2021
- **Enterprise adoption**: Used in Microsoft's identity products
- **Multiple implementations**: Reference impl + enterprise versions

### 3. Stability

**Stable / Production**

- **Version 1.0**: Released and operational
- **Proven at scale**: Processing real operations on mainnet
- **Active maintenance**: DIF oversight
- **Clear specification**: Well-documented protocol

## Special Considerations

### Ledger-Agnostic Design

The key insight from README.md is relevant here:
> "it is tempting to dismiss standards like `did:ion` because it is made for Bitcoin... However, a lot of these systems are actually ledger-agnostic under the hood"

Sidetree explicitly supports any anchoring system. We could implement a `did:ion`-compatible (or our own Sidetree-based) method using our ledger.

### Benefits of Sidetree Approach

- **Key rotation**: Update keys without changing DID
- **Recovery**: Emergency key recovery mechanism
- **Scalability**: Batching reduces per-operation costs
- **Deterministic**: No additional consensus needed

### Challenges

- **Implementation complexity**: Significant engineering effort
- **Storage requirements**: Need CAS infrastructure
- **Full history**: Resolution requires processing all operations
- **Operation ordering**: Must handle concurrent operations correctly

## Recommendation

**Requires investigation - High potential**

The Sidetree/ION approach deserves serious investigation:

1. **Ledger-agnostic**: Could use our ledger as anchor layer
2. **Full lifecycle**: Supports key rotation, recovery, deactivation
3. **Enterprise-ready**: Proven in production, Microsoft-backed
4. **Scalable**: Batching provides excellent throughput

**Investigation needed:**
- Effort to implement Sidetree anchor layer for our ledger
- CAS strategy (use IPFS vs. own implementation)
- Whether we'd register as `did:ion` variant or new method name
- Resource requirements for resolution (processing operation history)

**Alternative approaches:**
- Implement Sidetree ourselves (significant effort, maximum compatibility)
- Design simpler key rotation mechanism inspired by Sidetree concepts
- Start with did:key, add Sidetree-based method later

This is one of the few DID methods that could provide enterprise-grade key management while remaining decentralized. Worth deeper investigation.
