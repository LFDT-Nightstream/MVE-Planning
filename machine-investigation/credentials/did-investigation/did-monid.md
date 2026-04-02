# did:monid (MONiD)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://lianxi-tech.github.io/monid/) |
| Organization | LianXi-Tech |
| DID Format | `did:monid:<keccak256_pubkey_hash>` |

## Overview
MONiD DID is an Ethereum-based decentralized identifier method that combines blockchain registry with IPFS document storage and Torus Network for key management. The identifier is a keccak256 hash of the public key. Resolution queries an Ethereum smart contract to get an IPFS address, then retrieves the document from IPFS.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Ethereum blockchain for registry smart contract
- IPFS for DID document storage
- MONiD IPFS Gateway for document retrieval
- Torus Network for decentralized key management and social authentication

This triple-layer infrastructure (Ethereum + IPFS + Torus) creates significant dependencies.

### 2. Ecosystem
Integrates with Torus Network, which provides social authentication-based key management. This enables familiar login flows (Google, Facebook, etc.) for key recovery. Tested on Ethereum Rinkeby testnet (now deprecated).

### 3. Stability
The specification (Version 1.0.0, December 2020) may be dated, especially given:
- Rinkeby testnet has been deprecated
- Torus Network has evolved since 2020
- IPFS gateway dependency adds point of failure

## Recommendation
**No-go**

Requires Ethereum blockchain, IPFS, and Torus Network infrastructure. The triple-dependency and potentially outdated specification make this unsuitable for production use.
