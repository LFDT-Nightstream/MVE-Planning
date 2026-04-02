# did:unitrust (Unitrust)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/sheca-bc/did-unitrust-specification/blob/main/README.md) |
| Organization | Shanghai Electronic Certification Authority Co., Ltd. |
| DID Format | `did:unitrust:51uYnBT3KXnbHowvEn5ksG23quaJ` |

## Overview
did:unitrust is a DID method operating on the Shanghai Identity Chain, a distributed ledger for identity management in China. The method uses Chinese national cryptographic standards (SM2/SM3) and requires real-name digital certificates from certificate authorities for DID creation. DIDs are registered as blockchain-based trusted accounts with all CRUD operations recorded on-chain.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Multiple external dependencies:
- Requires the Shanghai Identity Chain blockchain infrastructure
- Mandates real-name digital certificates from authorized CAs
- Uses Chinese national cryptographic standards (SM2/SM3)
- All operations recorded on proprietary blockchain
- Resolution queries the blockchain for DID documents

### 2. Ecosystem
Regional ecosystem focused on China. Tied to Shanghai Electronic Certification Authority (SHECA), a government-affiliated certificate authority. Limited international adoption potential due to regional focus and certificate requirements.

### 3. Stability
Specification version 1.0.0, aligned with W3C DID Core v1.0. Documentation hosted at sheca.com. Stability depends on Chinese regulatory environment and SHECA's operations.

## Recommendation
**No-go**

Requires the Shanghai Identity Chain blockchain and real-name certificates from Chinese certificate authorities. Geographically and infrastructurally constrained.
