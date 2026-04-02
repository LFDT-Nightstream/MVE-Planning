# did:ssb (Secure Scuttlebutt)

| Property | Value |
|----------|-------|
| Specification | [Scuttlebot Viewer](https://viewer.scuttlebot.io/&5Bne/slGKH/i1361qemVlNBElWInSUfntlWvMXaD4M4=.sha256) |
| Organization | Charles E. Lehner |
| DID Format | `did:ssb:<feed-id>` |

## Overview
did:ssb enables SSB (Secure Scuttlebutt) users to manage DIDs corresponding to their SSB feed IDs. SSB is a peer-to-peer protocol for social applications based on append-only verifiable logs. DID documents are updated by publishing SSB messages of type `did-document-update`.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires SSB network participation:

- Resolution queries SSB network for `did-document-update` messages
- DID documents stored as SSB messages or blobs
- Requires SSB feed ID (cryptographic identity in SSB)
- Supports versionTime queries for historical states
- Peer-to-peer gossip protocol for data propagation

The specification is marked as a draft, not recommended for main SSB network until stabilized.

### 2. Ecosystem
**Small but dedicated.** SSB has a passionate community focused on decentralization and offline-first design. Limited mainstream adoption.

### 3. Stability
**Draft stage.** Explicitly marked as not ready for production use. Experimental implementation experience needed.

## Recommendation
**No-go**

Requires SSB peer-to-peer network for resolution and updates. The draft status and P2P network dependency make it unsuitable for self-contained operation.
