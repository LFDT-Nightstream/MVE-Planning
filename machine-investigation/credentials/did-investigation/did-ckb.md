# did:ckb (Nervos CKB DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:ckb Spec](https://github.com/web5fans/web5-wips/blob/master/01.md) |
| Organization | web5fans (Ian Yang, Cryptape) |
| DID Format | `did:ckb:<32-char-base32-identifier>` |

## Overview

The did:ckb method uses the Nervos CKB (Common Knowledge Base) blockchain's Cell model to store DID metadata. It uses a UTXO-based approach where DIDs are tied to "DID Metadata Cells" that can only be consumed once.

Example DID: `did:ckb:qq2m72a2vas4e5ovcpxoedscguuu4nba`

### DID Structure

- **Prefix**: `did:ckb:`
- **Identifier**: Exactly 32 characters, Base32 encoded (lowercase a-z and 2-7)
- **Derivation**: First 20 bytes of BLAKE2b hash of cell creation data

### Identifier Generation

1. Hash components (in order):
   - Input's since field (8 bytes)
   - First input's previous transaction hash (32 bytes)
   - First input's output index (4 bytes)
   - DID Metadata Cell output index (8 bytes)
2. BLAKE2b hash with personalization `ckb-default-hash`
3. Take first 20 bytes
4. Base32 encode

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Create DID Metadata Cell via CKB transaction |
| Resolve | Find live cell matching type script and args |
| Update | Consume existing cell, create new cell with updated metadata |
| Deactivate | Consume cell without creating replacement (permanent) |

### Data Format

- **Storage**: Molecule-encoded DidCkbData in cell data
- **Document**: DAG-CBOR encoded JSON
- **Compatibility**: Compatible with did:plc metadata format

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ckb method requires:

- **Nervos CKB blockchain**: All operations require CKB network access
- **Cell model**: Uses CKB's unique UTXO-variant storage model
- **Type scripts**: Validation via CKB's script system
- **Live cell search**: Resolution requires scanning for live cells

**Adaptation possibility**: Very low. The method is deeply tied to CKB's Cell model and script validation system. The UTXO-based approach is interesting but cannot be separated from CKB.

### 2. Ecosystem

**Niche / Nervos-specific**

- **Draft status**: Still in development
- **Single proposer**: Ian Yang (Cryptape)
- **Nervos ecosystem**: Limited to CKB users
- **did:plc compatibility**: Interesting cross-compatibility

### 3. Stability

**Early stage**

- **Status**: Draft specification
- **Active development**: Cryptape maintains
- **Nervos dependency**: Tied to Nervos CKB success

## Special Considerations

### Interesting Design Patterns

- **UTXO-based uniqueness**: Cell can only be consumed once
- **Type script validation**: Smart contract-style verification
- **Lock script authorization**: Flexible authorization model
- **Conflict resolution**: Earliest cell wins in case of conflicts

### did:plc Compatibility

- Shares metadata format with did:plc
- Can reuse did:plc SDK for document transformation
- Excludes PLC-specific fields (rotationKeys, prev, sig)

## Recommendation

**No-go**

The did:ckb method is unsuitable for our use case:

1. **CKB dependency**: Requires Nervos CKB blockchain access
2. **Cell model specific**: Cannot separate from CKB's unique storage model
3. **Niche ecosystem**: Limited adoption outside Nervos
4. **External state**: Cannot verify CKB state from our ledger

The UTXO-based approach and did:plc compatibility are interesting patterns but don't justify the integration complexity.
