# did:iden3 (Iden3)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/iden3/did-iden3/) |
| Organization | Iden3 |
| DID Format | `did:iden3:<blockchain>:<networkID>:<base58-identifier>` |

## Overview
The did:iden3 method is designed to maximize privacy using Zero Knowledge Proofs (ZKP) technology. Supports multiple blockchains (Polygon, Ethereum, Privado) with base58-encoded identifiers. Resolution queries smart contracts on EVM-compatible blockchains. Features Merkle Tree Proof integration for claim validity and revocation status. Uses Baby JubJub curve cryptography for ZK-friendly operations.

## Evaluation

### 1. Feasibility/Complexity
**Requires investigation for ZKP patterns.** Resolution requires external blockchain interaction (EVM-compatible chains), which is a no-go for direct use. However, the Zero Knowledge Proof architecture is highly interesting for ledger-agnostic patterns. The system uses state contracts for publishing identity states, and ZK proofs can prove claims without revealing underlying data.

Key technical elements worth studying:
- Genesis ID-based immutable identity foundation
- Merkle Tree Proof integration
- ZKP generation for privacy-preserving verification
- Both on-chain and off-chain identity types

### 2. Ecosystem
Growing ecosystem with production deployments on Polygon and Ethereum. Privado network provides dedicated infrastructure. JSON-LD contexts hosted at schema.iden3.io. Active development with focus on privacy-preserving credentials. Used by Polygon ID.

### 3. Stability
Well-documented specification with multiple blockchain deployments. The ZKP-based approach represents cutting-edge cryptographic techniques. Contract addresses are published for Privado networks. Active maintenance and ecosystem support.

## Recommendation
**Requires investigation**

While direct resolution requires blockchain access (no-go), the Zero Knowledge Proof architecture offers valuable patterns for privacy-preserving verification that could inform ledger-agnostic designs. The Merkle Tree and state commitment patterns may be adaptable.
