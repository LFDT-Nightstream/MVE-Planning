# did:stack (Blockstack/Stacks)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/blockstack/blockstack-core/blob/stacks-1.0/docs/blockstack-did-spec.md) |
| Organization | Jude Nelson / Blockstack |
| DID Format | `did:stack:v0:<address>-<index>` |

## Overview
did:stack is a DID method for the Blockstack (now Stacks) decentralized naming layer. It anchors to Bitcoin for security while using the Atlas peer network for off-chain data propagation. Names are registered via NAME_PREORDER and NAME_REGISTRATION transactions.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires multiple infrastructure components:

- Bitcoin blockchain for anchoring (selected for security)
- Atlas peer network for zone file propagation
- Blockstack node infrastructure for DID resolution
- REST API endpoints (`/v1/dids/{:blockstack_did}`)
- Secp256k1 cryptography for signatures

Supports both on-chain names (direct blockchain transactions) and off-chain subdomains (batched hashes).

### 2. Ecosystem
**Medium.** Stacks has an active ecosystem with Bitcoin integration focus. Production deployment with significant user base. Active development continues.

### 3. Stability
**Mature for v0.** The specification is well-documented. The broader Stacks ecosystem continues to evolve but the DID method is stable.

## Recommendation
**No-go**

Requires Bitcoin blockchain, Atlas peer network, and Blockstack node infrastructure. Multiple external dependencies make it unsuitable for self-contained operation.
