# did:mesh (Trusted Digital Web)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/mwherman2000/TrustedDigitalWeb/blob/master/specifications/did-methods/did-mesh-1-0-1.md) |
| Organization | Trusted Digital Web Project |
| DID Format | `did:mesh:<base64_keri_encoded_id>` |

## Overview
DIDMesh is a decentralized identifier method that uses KERI (Key Event Receipt Infrastructure) key management techniques for identifier generation. The method employs a distributed mesh approach for resolution through network nodes rather than blockchain consensus.

## Evaluation

### 1. Feasibility/Complexity
**Potentially self-contained with distributed node requirements.** Key characteristics:

- **KERI-based identifiers**: Uses KERI key management for self-certifying identifier generation
- **Distributed resolution**: `GetDIDDocument()` calls to distributed nodes rather than blockchain
- **No centralized registry**: Resolution recommends querying multiple nodes and comparing values
- **No external VDR required**: Self-governed through key material

The specification notes resolution without a "single source of truth" - instead relying on distributed mesh consensus.

### 2. Ecosystem
Part of the Trusted Digital Web project. The KERI foundation provides strong cryptographic principles. The mesh network approach requires node infrastructure but not blockchain.

### 3. Stability
The specification supports full CRUD operations plus key management (AddKey/RemoveKey). Multiple cryptographic algorithms supported (ECDSA, SM2, EdDSA). The reliance on KERI provides theoretical soundness.

## Recommendation
**Requires Investigation**

DIDMesh shares KERI's ledger-agnostic foundation but requires distributed mesh nodes for resolution. The KERI-based identifier generation is promising, but practical deployment requires understanding the mesh network requirements. Investigate:
- Minimum viable mesh network configuration
- Relationship to did:keri and potential code reuse
- Node deployment and synchronization requirements
