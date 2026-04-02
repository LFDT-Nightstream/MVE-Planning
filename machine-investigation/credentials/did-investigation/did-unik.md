# did:unik (Unikname)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/unik-name/did-method-spec/blob/main/did-unik/UNIK-DID-Specification.md) |
| Organization | Space Elephant SAS (France) |
| DID Format | `did:unik:158cffbe4d7b567468a17290c0cd1546ea3b013059a3a471e5ad309cfddfb0e3` |

## Overview
did:unik is a DID method for the Unikname ecosystem that represents human-readable identifiers (@unikname) as NFTs on the uns.network blockchain. The method-specific identifier is a 64-character hexadecimal hash (unik-id) that serves as the primary key for the NFT. DIDs cannot be self-controlled and require a did:uns controller associated with an uns.network crypto-account.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** The method is tightly coupled to the uns.network blockchain (built on ARK.io) and requires:
- Access to uns.network nodes for resolution
- The Unikname resolver service at resolver.uns.network
- uns.network crypto-accounts for DID control
- CLI or mobile app for management

### 2. Ecosystem
Small ecosystem. The project is maintained by Space Elephant SAS, a French company. Limited adoption outside the Unikname platform. The uns.network itself is a niche blockchain focused on NFT-based identifiers.

### 3. Stability
The specification is registered with W3C DID Specification Registries. However, the project depends on the continued operation of uns.network infrastructure and the Unikname company.

## Recommendation
**No-go**

Requires the uns.network blockchain infrastructure for all operations. Cannot function without external blockchain dependencies.
