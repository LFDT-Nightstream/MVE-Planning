# did:unisot (UNISOT)

| Property | Value |
|----------|-------|
| Specification | [GitLab](https://gitlab.com/unisot-did/unisot-did-method-specification) |
| Organization | UNISOT AS (Norway) |
| DID Format | `did:unisot:<method-specific-id>` |

## Overview
did:unisot is a DID method built on Bitcoin SV (BSV) blockchain, designed for enterprise supply chain management. UNISOT positions itself as a "Universal Source of Truth" for supply chain traceability, using BSV's high transaction throughput and immutable audit trails. The method leverages BSV smart contracts for identity verification.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** The method is entirely dependent on Bitcoin SV blockchain infrastructure:
- Requires BSV network access for resolution
- Uses BSV smart contracts for DID operations
- All DID documents are anchored to the BSV blockchain
- Transaction fees paid in BSV

### 2. Ecosystem
Niche ecosystem. UNISOT has W3C approval for their DID method and focuses on enterprise supply chain use cases. The BSV ecosystem itself is controversial and has limited adoption compared to other blockchains.

### 3. Stability
The specification is maintained on GitLab under Apache 2.0 license (created January 2021). Stability depends on both UNISOT AS and the Bitcoin SV network's continued operation.

## Recommendation
**No-go**

Requires Bitcoin SV blockchain infrastructure. Cannot operate independently without BSV network access.
