# did:etho (ETHO DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:etho Spec](https://github.com/ontology-tech/DID-method-specs/blob/master/did-etho/DID-Method-etho.md) |
| Organization | ontology-tech |
| DID Format | `did:etho:<40-hex-ethereum-address>` |

## Overview

The did:etho method is essentially identical to did:celo - another EVM-based DID method where Ethereum addresses automatically own corresponding DIDs without explicit registration.

Example DID: `did:etho:1f4B9d871fed2dEcb2670A80237F7253DB5766De`

### DID Structure

- **Prefix**: `did:etho:`
- **Identifier**: 40 hexadecimal characters (Ethereum address without 0x)

### Key Features

- **Implicit creation**: Any Ethereum address automatically has a DID
- **Smart contract registry**: Documents stored in contract
- **Controller management**: Add/remove delegates

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Automatic - address ownership = DID ownership |
| Read | `get_document()` on registry contract |
| Update | `add_controller()`, `remove_controller()` |
| Deactivate | `deactivate_did()` - permanent, cannot reactivate |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

Identical issues to did:ethr and did:celo:

- **Ethereum dependency**: Requires Ethereum network access
- **Smart contract state**: Document stored in contract
- **External chain state**: Cannot verify from our ledger

**Adaptation possibility**: None. Same architecture as did:ethr.

### 2. Ecosystem

**Limited / Duplicate approach**

- **ontology-tech**: Same organization as did:celo
- **Reference implementation**: DID-solidity repository
- **W3C compliant**: Follows standards
- **Redundant**: Very similar to did:ethr, did:celo

### 3. Stability

**Complete specification**

- **W3C CCG compliant**: Standards-based
- **Implementation available**: Reference code exists
- **Well-documented**: Clear operations

## Recommendation

**No-go**

The did:etho method is unsuitable for our use case:

1. **Ethereum dependency**: Same issue as did:ethr
2. **Smart contract state**: Cannot verify from our ledger
3. **Redundant**: Provides no advantage over did:ethr
4. **External blockchain**: Requires Ethereum access

This is functionally identical to did:ethr - same pattern, same limitations.
