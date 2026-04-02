# did:btcr (Bitcoin Reference DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:btcr Spec](https://w3c-ccg.github.io/didm-btcr/) |
| Organization | W3C CCG / Digital Contract Design |
| DID Format | `did:btcr:<txref>` |

## Overview

The Bitcoin Reference DID method (did:btcr) is one of the earliest DID methods, designed as a minimal, secure reference implementation using Bitcoin. DIDs are encoded as TxRef transaction references, and updates/revocations are managed through transaction chaining.

Example DID: `did:btcr:xyv2-xzpq-q9wa-p7t`

### DID Structure

- **Prefix**: `did:btcr:`
- **TxRef**: BIP-0136 Bech32-encoded transaction position (chain, blockheight, tx index)
- **Optional outpoint index**: For referencing specific transaction outputs

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Create Bitcoin tx with two key pairs, broadcast, wait for confirmation |
| Read | Extract TxRef, query blockchain, follow spent outputs to find current version |
| Update | Spend current output to new address with OP_RETURN pointing to new DID document |
| Delete | Spend output without OP_RETURN to revoke |

### Key Rotation via Transaction Chaining

Updates follow "the tip" - resolvers traverse the chain of spent outputs until finding an unspent output. Each transaction is signed by the previous transaction's key, creating a cryptographic audit trail. If the final unspent transaction has no OP_RETURN but has predecessors, the DID is revoked.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:btcr method requires Bitcoin blockchain access:

- **Bitcoin full node**: Resolution requires querying transaction history
- **UTXO tracking**: Must follow spending chain to find current DID state
- **Transaction costs**: Every update requires a Bitcoin transaction
- **Confirmation delays**: Must wait for block confirmations
- **OP_RETURN limits**: External document reference limited by OP_RETURN size

**Adaptation possibility**: Very low. The method is fundamentally based on Bitcoin's UTXO model and transaction graph. We cannot verify Bitcoin state from our ledger.

### 2. Ecosystem

**Historical / Reference**

- **W3C CCG**: Developed by W3C Credentials Community Group
- **Universal Resolver support**: Has driver in DIF Universal Resolver
- **Historical significance**: One of the first DID methods (2017)
- **Reference implementation**: Designed as conservative, secure example
- **Open source**: Fully open, not tied to commercial vendor
- **Limited current adoption**: Newer methods (did:ion, did:btc) more popular

### 3. Stability

**Stable but superseded**

- **Mature specification**: Well-documented, BIP-0136 finalized
- **Transaction costs**: Bitcoin fees make updates expensive
- **Scaling concerns**: UTXO inflation, blockchain bloat noted as issues
- **Superseded**: did:ion (Layer 2) and did:btc (Taproot) address limitations

## Special Considerations

- **Security model**: "As strong as Bitcoin itself"
- **Full audit trail**: All key rotations visible on-chain
- **Vendor agnostic**: Open source reference implementation
- **Minimal design**: Intentionally conservative
- **External documents**: DID Document stored off-chain, referenced via OP_RETURN

## Recommendation

**No-go**

The did:btcr method is unsuitable for our use case:

1. **Bitcoin dependency**: Requires Bitcoin blockchain access for resolution
2. **Cannot verify Bitcoin state**: Our ledger cannot query Bitcoin transactions
3. **Cost/scaling issues**: Transaction fees and UTXO bloat
4. **Superseded**: Newer Bitcoin DID methods (did:ion, did:btc) more practical

Historical value:
- **Pioneer method**: Important for DID ecosystem development
- **Design principles**: Minimal, secure, auditable - good reference
- **Transaction chaining**: Key rotation via spending is elegant

The design philosophy (minimal, vendor-agnostic, secure by default) is admirable, but Bitcoin dependency makes direct use impossible for our registry.
