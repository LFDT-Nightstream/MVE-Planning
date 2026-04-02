# did:theseries (The Series)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/theseriesteam/tsid-method/blob/main/ts-did-method-specification.md) |
| Organization | Hang Hang |
| DID Format | `did:theseries:[network:]<ethereum-address>` |

## Overview
did:theseries is a DID method using Ethereum-based smart contracts with ERC725 standard for identity. Supports multiple EVM-compatible networks including mainnet, testnets, private chains, and consortium chains. Uses secp256k1 cryptography.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Ethereum/EVM blockchain:

- ERC725 identity contracts for DID document storage
- Smart contract deployed on Sepolia: `0x47e6334374a7453B5fB22e37838E7B1d1e856BC4`
- Resolution via `resolveID()` and `getData()` contract calls
- Clients cache ERC725 contract addresses for efficiency
- HD wallet accounts for keypair management

### 2. Ecosystem
**Small.** Focused on The Series platform. Benefits from ERC725 standard adoption. Limited broader adoption.

### 3. Stability
**Early to moderate.** Documentation follows W3C standards. Depends on ERC725 standard stability.

## Recommendation
**No-go**

Requires Ethereum/EVM blockchain infrastructure for all operations. Cannot function without access to supported blockchain networks.
