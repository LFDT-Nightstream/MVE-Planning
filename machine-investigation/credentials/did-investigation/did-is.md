# did:is (Blockcore)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/block-core/blockcore-did-method) |
| Organization | Block-core |
| DID Format | `did:is:<P2PKH_address>` |

## Overview
Blockcore DID (did:is) is a decentralized identifier method built on Blockcore-based blockchains. Identity profiles are stored on nodes with the Storage feature enabled, using JWT-based documents with JSON Web Signatures. The method uses secp256k1 cryptography with BIP32/BIP44 hierarchical deterministic key derivation.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Blockcore-derived blockchain nodes with Storage feature enabled
- REST APIs on nodes or hubs for resolution
- The public hub at https://www.did.is/ for standard resolution

DID documents reside on blockchain nodes, making resolution dependent on network infrastructure.

### 2. Ecosystem
Limited ecosystem. Blockcore is a smaller blockchain project focused on building infrastructure for multiple blockchain networks. The DID method appears to have niche adoption within the Blockcore ecosystem.

### 3. Stability
The specification follows W3C standards and uses established cryptographic methods (secp256k1, JOSE). The cross-chain compatibility across Blockcore networks is a positive feature, but the overall ecosystem size limits stability guarantees.

## Recommendation
**No-go**

Requires Blockcore blockchain infrastructure for storage and resolution. Cannot function as a self-contained DID method.
