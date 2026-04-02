# did:plc (Bluesky PLC)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/bluesky-social/did-method-plc) |
| Organization | Bluesky PBLLC |
| DID Format | `did:plc:<base32-hash>` |

## Overview
did:plc is a self-authenticating DID method developed by Bluesky for the AT Protocol. It emphasizes strong consistency, recoverability, and key rotation capabilities. The identifier is derived from the hash of the genesis operation, creating a cryptographic chain of custody.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires a centralized directory server (plc.directory) that collects and validates operations and maintains a transparent log of operations for each DID. While the cryptographic verification is self-authenticating (each operation references prior state via hash), resolution depends on querying the PLC directory server.

Key features:
- Rotation keys enable key rotation without changing the DID
- Operations form a cryptographic chain starting from genesis
- Requires HTTP access to the PLC directory for resolution

### 2. Ecosystem
**Large and active.** did:plc is the primary DID method for Bluesky/AT Protocol, which has millions of users. Active development with TypeScript reference implementation (`@did-plc/lib`) and Golang implementation in progress. The ecosystem is growing rapidly with the expansion of Bluesky's social network.

### 3. Stability
**Stable for production use.** Currently at v0.1 specification. While technically still evolving, it's battle-tested in production with millions of DIDs on Bluesky. The specification is well-documented and the implementation is mature.

## Recommendation
**No-go**

While did:plc has an excellent design for key rotation and recovery, it fundamentally requires external HTTP resolution to the PLC directory server. This makes it unsuitable for a self-contained ledger-based registry that cannot depend on external infrastructure.
