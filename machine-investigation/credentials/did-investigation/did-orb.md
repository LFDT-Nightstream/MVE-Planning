# did:orb (Orb DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:orb Method v0.2](https://trustbloc.github.io/did-method-orb/) |
| Organization | SecureKey Technologies / TrustBloc |
| DID Format | `did:orb:<anchor-hash>:<did-suffix>` |

## Overview

The did:orb method creates a federated and replicated Verifiable Data Registry (VDR) where independent servers coordinate without a common blockchain. It builds on the Sidetree protocol to provide a "fediverse" of interconnected nodes and witnesses.

Example DID: `did:orb:uEiDlXjleTwr4eZalpXVy086zs-TPK-h54ojbpl7EBvZeHQ:EiDyOQbbZAa3aiRzeCkV7LOx3SERjjH93EXoIM3UoN4oWg`

### Architecture

The system uses three types of servers:
- **Writer servers**: Accept DID operations and create batches
- **Witness servers**: Provide timestamping and maintain partial ledger histories using RFC6962 (Certificate Transparency) style ledgers
- **Resolver servers**: Respond to DID resolution requests

### DID Format Variants

| Format | Structure |
|--------|-----------|
| Canonical | `did:orb:<anchor-hash>:<did-suffix>` |
| Long-form | `did:orb:<anchor-hash>:<did-suffix>:<did-suffix-data>` |
| Scheme-based | `did:orb:<scheme>:<path>:<anchor-hash>:<did-suffix>` |

### CRUD Operations

Operations follow the Sidetree protocol:
- **Create**: Generate self-certifying DID with initial PKI metadata
- **Update**: Delta-based modification of keys/services
- **Recover**: Replace cryptographic material using recovery key
- **Deactivate**: Permanently disable DID

### Propagation

Orb uses ActivityPub protocols for gossip-based propagation between servers, creating a graph of immutable references to prior batches.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible - Heavy external dependencies**

The did:orb method has fundamental architectural requirements that conflict with our constraints:

**Major blockers:**
- **HTTP-based resolution**: Resolution requires querying Orb server endpoints via HTTP(S)
- **Distributed server network**: Depends on a fediverse of interconnected Orb servers
- **ActivityPub propagation**: Server-to-server communication via ActivityPub
- **WebFinger discovery**: Uses `.well-known/did-orb` and WebFinger for endpoint discovery
- **Optional IPFS**: Content-addressable storage layer may use IPFS
- **Witness network**: Requires witness servers for timestamping and conflict resolution

**Self-certifying aspects:**
- Like Sidetree/ION, the DID suffix is self-certifying via cryptographic derivation
- Operations are authenticated via JSON Web Signatures
- The anchor-hash provides content integrity

**Critical limitation**: Unlike pure Sidetree implementations, Orb's federated model means resolution fundamentally requires network access to Orb servers. There is no way to verify DIDs offline without access to the distributed witness network.

### 2. Ecosystem

**Limited / Niche**

- **Single implementer**: Primarily maintained by SecureKey Technologies/TrustBloc
- **No major adoption data**: Specification mentions design goals but provides no deployment statistics
- **Limited tooling**: Not as widely supported as did:web, did:key, or did:ion
- **Enterprise focus**: Designed for interconnected organizational VDRs
- **Hyperledger connection**: TrustBloc is associated with Hyperledger Aries ecosystem

### 3. Stability

**Unofficial / Early Stage**

- **Status**: Marked as "unofficial" (no W3C or standards body ratification)
- **Version**: v0.2 (pre-1.0)
- **Dependencies**: References Internet-Drafts and Draft Community Group Reports
- **Active development**: Appears actively maintained but not production-stable

## Special Considerations

### Relationship to Sidetree

Orb builds on the Sidetree protocol (used by did:ion) but adds:
- Federated witness network instead of blockchain anchoring
- ActivityPub-based propagation
- Certificate Transparency-style ledgers for witnesses

This makes it more flexible than did:ion (no Bitcoin dependency) but adds complexity through the distributed server model.

### Known Limitations

The specification explicitly acknowledges several risks:
- **Data deletion vulnerability**: "If all copies of a VDR object are deleted, the associated DIDs become unresolvable"
- **Incomplete propagation**: Different servers hold "a subset of the overall transaction graph"
- **Light node constraints**: Servers may lose access to files if they become unavailable
- **DoS susceptibility**: Vulnerable to spam transaction announcements

### No Blockchain Requirement

Orb's key innovation is avoiding blockchain dependency while maintaining decentralization. However, this is achieved through a federated server model that still requires network connectivity.

## Recommendation

**No-go**

The did:orb method cannot be supported given our key constraint that nodes cannot make HTTP requests to validate DIDs.

**Primary reasons:**
1. **HTTP resolution required**: Resolution fundamentally requires HTTP(S) calls to Orb servers
2. **Federated server model**: The entire architecture depends on a network of interconnected servers
3. **Witness network dependency**: Conflict resolution and timestamp verification require witness server access
4. **No offline verification path**: Unlike did:key or even long-form did:ion, there is no self-contained verification mechanism

**Contrast with did:ion**: While did:ion also uses Sidetree, its anchoring to Bitcoin means you could theoretically include anchor proofs. Orb's federated model has no single source of truth that could be embedded in transactions.

**If federated resolution were acceptable**: The Sidetree foundation and key rotation capabilities would make this an interesting option. But the core architecture is incompatible with our offline verification requirement.
