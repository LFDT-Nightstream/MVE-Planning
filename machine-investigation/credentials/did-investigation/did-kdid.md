# did:kdid (FISCO BCOS / Kingdom Fintech)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/KingdomFintechBJ-BC/kdid/blob/main/doc/en/kdid.md) |
| Organization | Kingdom Fintech Beijing |
| DID Format | `did:kdid:<bst>:<suffix>` |

## Overview
KDID is a decentralized identifier method built on FISCO BCOS blockchain, targeting financial sector applications and cross-agency identity authentication. DIDs are stored on-chain via smart contracts, with optional regional/blockchain identifiers (bst) in the format.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- FISCO BCOS blockchain infrastructure
- Smart contract deployment for DID document management
- Blockchain nodes for document storage and resolution
- SM2 elliptic curve cryptography support (Chinese national standard)

All operations are performed through blockchain smart contracts.

### 2. Ecosystem
Specialized ecosystem focused on Chinese financial sector. FISCO BCOS is a consortium blockchain platform with adoption in Chinese enterprise environments. The use of SM2 cryptography aligns with Chinese regulatory requirements.

### 3. Stability
The specification follows W3C DID v1 standards for JSON-LD serialization. The focus on financial applications suggests regulatory compliance requirements, but the ecosystem is regionally focused.

## Recommendation
**No-go**

Requires FISCO BCOS blockchain infrastructure. Specifically designed for Chinese financial sector with SM2 cryptography requirements. Cannot function without the underlying consortium blockchain.
