# did:gwm (Great Wall Motors)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/GreatWall-Blockchain/GWDID-Method-Specification/blob/main/README.md) |
| Organization | Great Wall Motors (GWBaaS) |
| DID Format | `did:gwm:<router-id>:<0x-40-hex-identifier>` |

## Overview
The did:gwm method is developed by Great Wall Motors as part of their Great Wall Blockchain Service Platform (GWBaaS). It uses the ChainMaker blockchain network as a verifiable data registry, operating as a consortium chain with node access permission control. The system is designed for vehicle component traceability and cross-organization identity exchange.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires blockchain interaction with the ChainMaker consortium network, which has node access permission controls requiring cloud account credentials (AK/SK). The system supports multiple blockchain platforms (Chainmaker, FISCO-BCOS, Fabric) but all require external network access.

### 2. Ecosystem
Enterprise-focused ecosystem within the Great Wall Motors automotive supply chain. Highly specialized for vehicle traceability scenarios. Cross-platform support (multiple blockchain backends) shows flexibility but also indicates dependency on external infrastructure.

### 3. Stability
Commercial-grade specification developed by a major automotive manufacturer. However, the system is designed for permissioned consortium use rather than public decentralized identity. Tied to specific enterprise infrastructure.

## Recommendation
**No-go**

Requires access to the ChainMaker consortium blockchain with appropriate cloud credentials. Node operators cannot verify DIDs without authenticated access to Great Wall's blockchain infrastructure.
