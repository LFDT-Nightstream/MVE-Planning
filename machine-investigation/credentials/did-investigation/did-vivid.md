# did:vivid (Vivid / Moonlight)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/Moonlight-io/specs/blob/master/did-method-spec.md) |
| Organization | Moonlight.io |
| DID Format | `did:vivid:znWRVY1GSUSjJEFqD8hRBc6s68qkQ9MGsaSRNZc8ZaMX` |

## Overview
did:vivid is a DID method from Moonlight.io, designed as a platform-agnostic Self-Sovereign Identity (SSI) solution. The identifier is 16-128 base58-encoded characters. The method supports multiple DLT platforms including Neo2, Neo3, and Zilliqa. Operations are performed through the asteroid-sdk interface. Note: all DID document states are permanently stored on-chain due to blockchain immutability.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires external blockchain infrastructure:
- Resolution via asteroid-sdk smart contract interfaces
- CRUD operations: CreateIdentity, ReadIdentity, UpsertVerification, DeleteVerification
- Multi-chain support (Neo2, Neo3, Zilliqa) but still requires blockchain access
- DID documents stored immutably on the underlying ledger platforms
- Cannot function without one of the supported blockchain networks

### 2. Ecosystem
Niche ecosystem within the Neo and Zilliqa communities. Moonlight.io positions itself as a decentralized workforce platform. The multi-chain approach provides some flexibility but still requires blockchain infrastructure.

### 3. Stability
The specification warns that all DID document states are available "in perpetuity" and explicitly recommends against storing personally-identifiable information. This immutability concern is a design trade-off.

## Recommendation
**No-go**

Requires Neo2, Neo3, or Zilliqa blockchain infrastructure. While platform-agnostic across these chains, it cannot operate without external blockchain access.
