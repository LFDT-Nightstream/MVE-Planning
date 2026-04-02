# did:bsv (Bitcoin SV DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:bsv Spec](https://github.com/teranode-group/nChain-Identity-bsvdid-method) |
| Organization | nChain / Teranode |
| DID Format | `did:bsv:<transaction-id>` |

## Overview

The did:bsv method uses Bitcoin SV blockchain as the verifiable data registry, leveraging UTXO transactions for DID management. The transaction ID becomes the DID, and spending/unspending states control DID status.

Example DID: `did:bsv:<64-char-txid>`

### DID Structure

- **Prefix**: `did:bsv:`
- **Identifier**: Transaction ID (TxID) from the issuance transaction

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Publish transaction (Tx0) with single input/output; TxID becomes DID |
| Read | Query transaction, check UTXO spending status |
| Update | Spend current UTXO, create new transaction |
| Revoke | Spend UTXO to invalidate DID |

### Technical Design

- **UTXO-based**: DID status tied to transaction output spending state
- **Multi-signature**: Coordinated signatures between DID Subject and Controller
- **DID Manager**: Service component for signature coordination
- **Confirmation configurable**: Issuers/verifiers set required block confirmations

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bsv method requires Bitcoin SV blockchain:

- **BSV blockchain required**: All operations on Bitcoin SV network
- **UTXO state dependency**: Must query BSV to determine if outputs are spent
- **Full node or API needed**: Resolution requires blockchain access
- **Confirmation requirements**: Configurable block confirmations for security

**Adaptation possibility**: Low. While the UTXO-based concept could theoretically work on any UTXO chain, we cannot verify BSV state from our ledger.

The specification notes it "can be implemented in any UTXO-based blockchain" which is interesting, but practical use requires access to that blockchain.

### 2. Ecosystem

**Limited / Controversial**

- **BSV ecosystem**: Bitcoin SV has controversial history and limited adoption
- **nChain development**: Corporate-backed development
- **Limited tooling**: Not widely supported in mainstream DID tooling
- **No Universal Resolver driver**: Not integrated with DIF Universal Resolver

### 3. Stability

**Uncertain**

- **BSV chain stability**: BSV has had controversial governance
- **Corporate control**: nChain/Craig Wright associated
- **Limited community**: Small developer community
- **Specification complete**: Technical spec is comprehensive

## Special Considerations

- **Low fees**: BSV emphasizes low transaction costs
- **High throughput**: BSV designed for scalability
- **First-seen rule**: Miners honor first transaction, reducing double-spend risk
- **No PII on-chain**: DID Documents don't store personal information

## Recommendation

**No-go**

The did:bsv method is unsuitable for our use case:

1. **Bitcoin SV dependency**: Requires BSV blockchain access
2. **Controversial ecosystem**: BSV has reputation/governance concerns
3. **Limited adoption**: Small ecosystem, minimal tooling support
4. **Same fundamental issue**: Cannot verify external blockchain state

The UTXO-based DID management concept is interesting (DID status from spending state), but the BSV-specific implementation and ecosystem concerns make this impractical.
