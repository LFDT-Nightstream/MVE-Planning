# did:dime (Dimecoin DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:dime Spec](https://github.com/dime-coin/dimedid-method) |
| Organization | dime-coin |
| DID Format | `did:dime:<transaction-id>` |

## Overview

The did:dime method uses UTXO transaction models on the Dimecoin blockchain. DIDs are identified by transaction IDs, and the status of DID documents is tracked through transaction spending patterns.

Example DID: `did:dime:21f2dae26817752b8f92c51a49a898e287ad133a4e7ed64b4909f7b62f0bbb6e`

### DID Structure

- **Prefix**: `did:dime:`
- **Identifier**: Transaction ID (TxID) from blockchain

### Transaction Model

- **Tx0**: Issuance transaction (TxID becomes the DID)
- **Tx1**: Contains the DID Document (spends Tx0)
- **Status**: Determined by spending patterns of Tx1 output

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Create Tx0 (issuance) then Tx1 (document) with dual signatures |
| Resolve | Search ledger by TxID, trace linked transactions, check UTXO status |
| Update | New transaction spends most recent DID Document output |
| Destroy | Transaction spending indicates deactivation |

### Supported Algorithms

- SECP256k1
- JsonWebKey2020 verification method format

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dime method requires:

- **Dimecoin blockchain**: All operations require Dimecoin network access
- **UTXO scanning**: Resolution requires scanning blockchain for transactions
- **Transaction fees**: Requires Dimecoin tokens for operations
- **Dual signatures**: Both Controller and Subject must sign

**Adaptation possibility**: Low. While the UTXO pattern is interesting and claimed to work on any UTXO chain, we would still need to run our own UTXO chain or verify external chain state.

### 2. Ecosystem

**Minimal**

- **Unofficial draft**: v0.1, no official standing
- **Single author**: Douglas Hopping
- **Minimal adoption**: 0 stars, 0 forks
- **Dimecoin niche**: Very small cryptocurrency

### 3. Stability

**Early stage / Unstable**

- **Draft status**: Unofficial, subject to change
- **November 2024**: Very recent specification
- **Minimal community**: No apparent adoption

## Special Considerations

### Interesting Patterns

- **UTXO-based status**: Transaction spending indicates document state
- **Controller/Subject separation**: Dual-signature model
- **Transaction chain**: Linked transactions for history

### Claimed Generality

The spec claims applicability to any UTXO blockchain (Bitcoin, Litecoin, Cardano, etc.) but implementation complexity remains.

## Recommendation

**No-go**

The did:dime method is unsuitable for our use case:

1. **Blockchain dependency**: Requires Dimecoin (or other UTXO chain) access
2. **Minimal ecosystem**: No adoption, single author
3. **Early stage**: Unofficial draft with no implementations
4. **Scanning requirements**: UTXO scanning for resolution is complex

The UTXO-based status tracking is an interesting pattern but doesn't justify the integration complexity.
