# did:id (Mastercard ID Service)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/Mastercard/did-methods/blob/master/id.md) |
| Organization | Mastercard International Inc. |
| DID Format | `did:id:<base58-uuid>[:environment]` |

## Overview
The did:id method is operated by Mastercard as part of their ID Service (MIDS) platform for online identity verification and reusable digital identity. DIDs use base58-encoded UUIDs (21-22 characters) with Bitcoin/IPFS hash alphabet. Identity material and keys are generated and stored on mobile devices in Trusted Execution Environments (TEE). DIDs are immutable but documents can be updated for key rotation.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution uses HTTP-based retrieval from Mastercard's servers (`https://idservice.com/did/{did}`). This is a centralized resolution model despite the decentralized identifier format. TLS 1.2+ with 2048-bit encryption secures transport but doesn't address the centralization concern.

### 2. Ecosystem
Large ecosystem backed by Mastercard's global payment network and brand. Enterprise-grade security with TEE-based key storage on mobile devices. Designed for consumer digital identity verification within Mastercard's ecosystem. Strong commercial backing but proprietary infrastructure.

### 3. Stability
Production-grade specification from a major financial institution. Well-documented with clear security requirements. However, entirely dependent on Mastercard's continued operation of the ID Service platform.

## Recommendation
**No-go**

Requires HTTP resolution against Mastercard's centralized servers. Node operators cannot verify DIDs without external network requests to idservice.com, creating a dependency on Mastercard's infrastructure.
