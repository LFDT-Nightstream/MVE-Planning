# did:tangle (TangleID/IOTA)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/TangleID/TangleID/blob/develop/did-method-spec.md) |
| Organization | BiiLabs Co., Ltd. |
| DID Format | `did:tangle:<81-char-tryte-string>` |

## Overview
did:tangle implements DIDs on the IOTA Tangle distributed ledger. It uses Masked Authenticated Messaging (MAM) channels to store DID documents, with the initial channel ID as the DID identifier. Documents are appended as messages to a cryptographically secured queue.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires IOTA Tangle:

- Documents stored in MAM channels on IOTA network
- Uses Merkle-tree and Winternitz signatures
- Resolution queries Tangle for latest document in channel
- Updates append new messages to the queue
- Delete sends empty object to revoke channel access
- Zero transaction fees (IOTA feature)

### 2. Ecosystem
**Small.** Part of IOTA ecosystem. BiiLabs is an active IOTA development company. Limited adoption outside IOTA community.

### 3. Stability
**Moderate.** Well-documented specification. Depends on IOTA protocol stability (which has undergone significant changes).

## Recommendation
**No-go**

Requires IOTA Tangle network for all operations. Cannot function without IOTA network connectivity and MAM channel infrastructure.
