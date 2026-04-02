# did:tdid (Tencent TDID)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/TencentCloud-Blockchain/TDID-Method-Specification/blob/main/README.md) |
| Organization | Tencent Technology |
| DID Format | `did:tdid:<router-id>:<0x + 40-hex-chars>` |

## Overview
did:tdid is Tencent Cloud's DID method using consortium blockchain networks (BCOS, Chainmaker, FISCO-BCOS, Fabric). The identifier derives from public key using Ethereum-like address conversion. Requires Tencent Cloud account and credentials.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires extensive Tencent Cloud infrastructure:

- BCOS blockchain network as verifiable data registry
- Tencent Cloud account and TDID service subscription
- AK/SK authentication for all API operations
- Node access permission control required
- Can use escrow or self-hosted private keys

### 2. Ecosystem
**Medium in China.** Tencent has significant reach in Chinese enterprise market. Multiple blockchain platform support. Limited international accessibility.

### 3. Stability
**Enterprise-grade.** Backed by Tencent with enterprise support. Well-documented API. Regional focus on Chinese market.

## Recommendation
**No-go**

Requires Tencent Cloud infrastructure, authentication credentials, and consortium blockchain access. Heavily vendor-locked and not suitable for self-contained or internationally accessible use.
