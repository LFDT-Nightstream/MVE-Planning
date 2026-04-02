# did:kr (Korea Mobile Identity)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/identify202020/did-method/blob/main/did_kr_method.md) |
| Organization | Ministry of the Interior and Safety, Korea |
| DID Format | `did:kr:<identifier>` or `did:kr:<domain>:<identifier>` |

## Overview
Korea Mobile Identity DID is a government-operated decentralized identifier method for the Korean mobile identity system. DIDs are generated and managed through blockchain smart contracts operated by government-trusted nodes, using Delegated Proof of Stake consensus with BFT (2/3 threshold).

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Government-operated blockchain network
- Smart contracts for DID document management
- Trusted blockchain nodes (government-operated)
- Delegated Proof of Stake consensus mechanism
- RESTful API endpoints for CRUD operations
- TLS for traffic security

The infrastructure is specifically tied to Korean government operations.

### 2. Ecosystem
Government-backed ecosystem for Korean citizens and organizations. Designed for mobile identity verification in the Korean regulatory context. Limited applicability outside Korea.

### 3. Stability
Government backing provides institutional stability within Korea. The use of nonce-based replay attack prevention and digital signatures follows security best practices. However, the regional focus and government control limit broader applicability.

## Recommendation
**No-go**

Requires Korean government-operated blockchain infrastructure. Not applicable for general use cases outside the Korean identity system.
