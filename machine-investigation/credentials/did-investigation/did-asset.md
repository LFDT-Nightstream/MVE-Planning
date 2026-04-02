# did:asset (Asset DID Method)

| Property | Value |
|----------|-------|
| Specification | [Asset DID Spec](https://github.com/KILTprotocol/spec-asset-did) |
| Organization | KILT Protocol |
| DID Format | `did:asset:<caip2-chain>.<asset-namespace>:<asset-ref>[:<asset-id>]` |

## Overview

The did:asset method is a chain-agnostic system for identifying assets (tokens, NFTs) across any blockchain. It's a generative method - DIDs are derived from asset information rather than explicitly created. This follows the CAIP (Chain Agnostic Improvement Proposals) standards.

Example DIDs:
- `did:asset:eip155:1.erc721:0x06012c8cf97bead5deae237070f9587f8e7a266d:634446` (CryptoKitty NFT)
- `did:asset:bip122:000000000019d6689c085ae165831e93.slip44:0` (Bitcoin)

### DID Structure

- **Chain ID** (CAIP-2): `<namespace>:<reference>` (e.g., `eip155:1` for Ethereum mainnet)
- **Asset namespace**: 3-8 characters (e.g., `erc721`, `erc20`, `slip44`)
- **Asset reference**: 1-64 characters (e.g., contract address)
- **Asset ID** (optional): 1-78 characters (specific token ID)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generative - not explicitly created, derived from asset |
| Read | Decompose DID, query appropriate chain for asset data |
| Update | Not supported (generative method) |
| Deactivate | Not supported (generative method) |

### Supported Namespaces

- **Chains**: EIP155 (Ethereum-based), BIP122 (Bitcoin-based)
- **Assets**: SLIP44 (native tokens), ERC20 (fungible tokens), ERC721 (NFTs)

## Evaluation

### 1. Feasibility/Complexity

**Interesting concept but not directly applicable**

The did:asset method has a unique approach:

- **Generative method**: DIDs are derived from asset information, not created
- **Chain-agnostic design**: Uses CAIP standards to identify assets across chains
- **No write operations**: Purely resolves to asset metadata
- **Multi-chain dependency**: Resolution requires querying the specific blockchain where the asset lives

**Adaptation possibility**: Low for direct use, but the concept is interesting. We could potentially:
- Use a similar pattern to identify smart contracts on our ledger
- Adopt the CAIP identifier format for interoperability

However, the method fundamentally requires querying external blockchains to resolve asset information.

### 2. Ecosystem

**Growing / Standards-based**

- **KILT Protocol**: Well-established organization in DID/VC space
- **CAIP standards**: Built on widely-adopted chain-agnostic standards
- **Cross-chain focus**: Designed for multi-chain interoperability
- **Version 1.0**: Stable initial release (July 2022)

### 3. Stability

**Stable initial version**

- **Version 1.0**: First stable release
- **Standards-based**: Built on established CAIP specifications
- **Well-defined scope**: Clear limitations (no updates, no deactivation)
- **KILT maintenance**: Backed by established organization

## Special Considerations

- **Not for entities**: Identifies assets (tokens, NFTs), not people or organizations
- **Generative nature**: DIDs exist for assets that haven't been created yet (useful for "right to create" credentials)
- **Read-only**: No state changes possible through the DID method itself
- **CAIP interoperability**: Uses emerging standards for cross-chain identification

## Recommendation

**No-go (but informative)**

The did:asset method doesn't fit our use case for several reasons:

1. **Asset identification, not entity identity**: Designed for tokens/NFTs, not for managing entity identities
2. **Multi-chain dependency**: Requires querying external blockchains for resolution
3. **No key management**: Assets don't have keys - this isn't a key-based identity system
4. **Read-only**: Cannot create, update, or revoke DIDs

However, the design patterns are worth noting:
- **CAIP standards** for chain-agnostic identification could inform how we reference external assets
- **Generative DIDs** that don't require explicit creation is an interesting model
- If we need to reference NFTs or tokens in our system, did:asset provides a standardized way to do so
