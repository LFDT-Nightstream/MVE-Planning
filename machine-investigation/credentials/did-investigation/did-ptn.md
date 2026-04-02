# did:ptn (PalletOne)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/palletone/palletone-DID/blob/master/docs/did-method/README.md) |
| Organization | PalletOne |
| DID Format | `did:ptn:<method-specific-id>` |

## Overview
did:ptn is a DID method for the PalletOne blockchain project. It combines blockchain integration with Sidetree protocol functionality. The specification is still in early development (v0.1).

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires PalletOne blockchain infrastructure:

- References "PalletOne blockchain project" for mainnet operations
- Implements Sidetree protocol standard
- Uses blockchain browser for verification
- Sidetree node specifications indicate distributed ledger dependency

### 2. Ecosystem
**Very small.** Limited to PalletOne blockchain community. Minimal documentation and unclear adoption status.

### 3. Stability
**Early stage.** Version 0.1 specification. Documentation is sparse and project activity appears limited.

## Recommendation
**No-go**

Requires PalletOne blockchain infrastructure and Sidetree nodes for operation. Not suitable for self-contained use.
