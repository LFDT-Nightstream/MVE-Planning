# did:bit (.bit Domain DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:bit Spec](https://github.com/dotbitHQ/did-bit-spec) |
| Organization | .bit (dotbit) |
| DID Format | `did:bit:[network:]<name.bit>` |

## Overview

The did:bit method provides decentralized identifiers based on .bit domain names, a cross-chain naming system built on Nervos CKB blockchain. It's similar to ENS (Ethereum Name Service) but cross-chain compatible.

Example DIDs:
- `did:bit:example.bit`
- `did:bit:mainnet:example.bit`

### DID Structure

- **Prefix**: `did:bit:`
- **Network** (optional): `mainnet`, `pudge`, etc. (defaults to mainnet)
- **Name**: Registered .bit domain name

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Register .bit domain through .bit platform |
| Read | Resolve .bit name, retrieve CUSTOM records, generate DID document |
| Update | Modify .bit CUSTOM records via .bit platform |
| Delete | Domain expiration triggers reclamation |

### Resolution Process

1. Resolve .bit name on Nervos CKB
2. Retrieve DID-specific CUSTOM records
3. Generate default verification method from owner's `blockchainAccountId`
4. Create Web3PublicProfile service endpoint
5. Return W3C-compliant DID document

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bit method depends on Nervos CKB and .bit infrastructure:

- **Nervos CKB required**: .bit data stored on Nervos blockchain
- **.bit platform dependency**: Domain registration and management through .bit
- **Cross-chain complexity**: .bit supports multiple blockchains for ownership
- **No explicit key rotation**: Specification doesn't define rotation mechanism

**Adaptation possibility**: Very low. Resolution requires querying Nervos CKB blockchain and .bit infrastructure. We cannot verify .bit domains without access to these systems.

### 2. Ecosystem

**Growing / Web3 naming**

- **.bit platform**: Active cross-chain naming service
- **Nervos ecosystem**: Built on Nervos CKB blockchain
- **Web3 focus**: Designed for decentralized web identity
- **Draft status**: Specification still in draft

### 3. Stability

**Draft / Evolving**

- **Draft specification**: Not finalized
- **No key rotation**: Missing key management features
- **Platform dependent**: Tied to .bit platform evolution
- **Domain expiration**: DIDs can be "deleted" through domain reclamation

## Special Considerations

- **Human-readable names**: Uses .bit domain names as identifiers
- **Cross-chain ownership**: .bit accounts can be controlled from multiple chains
- **Web3PublicProfile**: Default service endpoint for Web3 identity
- **DDOS resistance**: Nervos CKB provides inherent protection

## Recommendation

**No-go**

The did:bit method is unsuitable for our use case:

1. **Nervos CKB dependency**: Requires querying Nervos blockchain
2. **.bit platform lock-in**: Domain management through centralized platform
3. **Draft specification**: Not yet finalized
4. **Missing features**: No explicit key rotation mechanism

The human-readable naming concept is valuable, but the dependency on external blockchain and naming infrastructure makes this impractical for our registry.
