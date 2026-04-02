# did:polygonid (Polygon ID / iden3)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/iden3/did-iden3/) |
| Organization | ZK ID Labs AG / Privado ID |
| DID Format | `did:iden3:<blockchain>:<networkID>:<base58-id>` |

## Overview
did:polygonid (also known as did:iden3) implements the iden3 protocol, which leverages zero-knowledge proofs for privacy-preserving identity. Identities are built from a Genesis ID (initial identity state hash) with state transitions verified through zkSNARKs. Claims use EdDSA on Baby JubJub curve.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires blockchain for most operations:

- State transitions must be published on-chain with ZK proof validation
- Smart contracts verify Groth16 zkSNARKs for identity state changes
- Resolution queries smart contract methods (getStateInfoById, getGISTRoot)
- Supports Polygon, Ethereum, and Privado networks

Special case: "readonly" identities not linked to any network cannot change state.

### 2. Ecosystem
**Growing.** Part of the Polygon ecosystem with focus on ZK-based identity. Active development with sophisticated privacy features. Used in various Polygon ID applications.

### 3. Stability
**Moderate.** The iden3 protocol is technically sophisticated but still evolving. Strong focus on ZK cryptography makes it complex to implement.

## Recommendation
**No-go**

Requires blockchain infrastructure for state management and ZK proof verification. The dependency on on-chain smart contracts for identity state transitions makes it unsuitable for self-contained operation.
