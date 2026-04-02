# did:infra (InfraBlockchain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/InfraBlockchain/infra-did-method-specs/blob/main/docs/Infra-DID-method-spec.md) |
| Organization | InfraBlockchain |
| DID Format | `did:infra:<network-id>:<pubkey-or-account>` |

## Overview
The did:infra method operates on InfraBlockchain, an EOSIO-based blockchain designed for public sectors and enterprises without cryptocurrency. Supports two DID types: public-key-based (lightweight, minimal blockchain transactions) and account-based (leveraging EOSIO's built-in key management). Public-key DIDs require no registration until modifications occur, providing minimal on-chain footprint.

## Evaluation

### 1. Feasibility/Complexity
**Partially interesting for investigation.** Resolution requires accessing InfraBlockchain network nodes to query DID registry smart contracts. However, the public-key-based DID type has an interesting property: it requires no blockchain registration until modifications occur. This suggests that basic DID verification might be possible from the DID itself (similar to did:key) for unmodified DIDs.

Key aspects:
- Two DID types with different trade-offs
- Minimal on-chain footprint for public-key DIDs
- Nonce mechanisms for replay attack prevention
- Service endpoints for customizable discovery

### 2. Ecosystem
Enterprise and public sector focused ecosystem. InfraBlockchain's "no cryptocurrency" design targets government adoption. EOSIO foundation provides technical maturity. JavaScript-compatible resolver available (infra-did-resolver).

### 3. Stability
Documented specification with clear separation of DID types. EOSIO-based architecture is technically mature. However, the specific InfraBlockchain network is less widely deployed than mainstream EOSIO chains.

## Recommendation
**Requires investigation**

The public-key-based DID type that requires no registration until modification is interesting for ledger-agnostic patterns. This hybrid approach (self-contained verification for basic DIDs, blockchain for modifications) may offer useful design insights. Worth examining how verification works for unmodified public-key DIDs.
