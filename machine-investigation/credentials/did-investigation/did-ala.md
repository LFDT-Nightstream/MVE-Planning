# did:ala (Alastria DID Method)

| Property | Value |
|----------|-------|
| Specification | [Alastria Identity Wiki](https://github.com/alastria/alastria-identity/wiki/Alastria-DID-Method-Specification-(Quorum-version)) |
| Organization | Alastria Consortium (Spain) |
| DID Format | `did:ala:<network_type>:<network_id>:<proxy_address>` |

## Overview

The Alastria DID method is a self-sovereign identity system developed by the Alastria Consortium, a Spanish nonprofit blockchain consortium. It is designed to facilitate compliance with GDPR and operates on permissioned blockchain networks.

Example DIDs:
- `did:ala:quor:redT:8d6a04959dd35e97c599ad907817a1a12fd70f96`
- `did:ala:besu:redB:8b76ed646df9da691c5e534f973b2e12bd40cce4`

### DID Structure

- **Prefix**: `did:ala:`
- **Network type**: `quor` (GoQuorum) or `besu` (Hyperledger Besu)
- **Network ID**: e.g., `redT`, `redB`
- **Proxy address**: Ethereum-style address (40 hex characters)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Call `PrepareAlastriaId` then `DelegateCall` in IdentityManager contract to create proxy |
| Read | Query smart contracts via Universal Resolver driver |
| Update | Smart contract interactions for key management |
| Deactivate | Smart contract state changes |

### Technical Infrastructure

- **Networks**: Alastria-T (GoQuorum, 200+ nodes, IBFT consensus) and Alastria-B (Hyperledger Besu)
- **Smart Contracts**: Solidity ^0.5.17, using OpenZeppelin Eternal Storage Pattern
- **Permissioned**: Public-permissioned network managed by Alastria partners

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ala method is tightly coupled to Alastria's permissioned blockchain infrastructure:

- **Specific blockchain required**: Requires either GoQuorum (Alastria-T) or Hyperledger Besu (Alastria-B) networks
- **Smart contract dependency**: Identity management relies on deployed contracts (IdentityManager, PublicKeyRegistry)
- **Permissioned network**: Alastria is a consortium network requiring membership
- **Spanish jurisdiction focus**: Designed specifically for Spanish legal compliance and GDPR

**Adaptation possibility**: Very low. The method is fundamentally designed around Alastria's specific permissioned blockchain infrastructure. We would need to either:
1. Join the Alastria consortium and run nodes
2. Completely reimplement the smart contract architecture on our ledger

Neither option makes sense given the regional/jurisdictional focus of Alastria.

### 2. Ecosystem

**Regional / Niche**

- **Spanish consortium**: Primarily used by Spanish organizations (banks, telecoms, government)
- **90+ partners**: Significant consortium membership within Spain
- **Universal Resolver support**: Has a driver for the DIF Universal Resolver
- **Limited international adoption**: Focus on Spanish/EU regulatory compliance limits broader adoption
- **Active development**: Continued development of MVP versions and network expansion

### 3. Stability

**Moderately stable within its scope**

- **Consortium governance**: Changes go through Alastria's Identity Commission
- **Multiple versions**: MVP1, MVP2 versions indicate evolution
- **Well-documented**: Detailed wiki and technical schemas
- **Regional focus**: Stability tied to Spanish regulatory environment

## Special Considerations

- **GDPR compliance**: Specifically designed for EU data protection compliance
- **Permissioned model**: Unlike public blockchain DIDs, requires consortium membership
- **Proxy pattern**: Uses proxy addresses for identity, enabling upgradability
- **Multi-network**: Supports both GoQuorum and Hyperledger Besu implementations

## Recommendation

**No-go**

The did:ala method is unsuitable for our use case for several reasons:

1. **Blockchain dependency**: Requires access to Alastria's specific permissioned networks (GoQuorum or Besu)
2. **Consortium membership**: Practical use requires joining the Alastria consortium
3. **Regional focus**: Designed specifically for Spanish organizations and EU/GDPR compliance
4. **Smart contract architecture**: Would require reimplementing their entire contract system

While Alastria has built a robust identity system for their use case, it's fundamentally a regional, consortium-specific solution that doesn't translate to a general-purpose DID method we could support.
