# did:real (Real Items)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/realitems/did-real/blob/main/did-real-method-specification.md) |
| Organization | Real Items |
| DID Format | `did:real:<ethereum-address>` |

## Overview
did:real is a DID method for Real Items, using Ethereum addresses as identifiers. DID documents are managed via a registry smart contract on Ethereum. The method is intentionally simple with no update capability.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Ethereum blockchain:

- DID documents stored in registry smart contract
- Create via `createDid` transaction
- **Updates not supported** - documents are immutable
- Deactivation is permanent and irreversible
- Resolution by querying `resolveDidDocument` method

Uses EcdsaSecp256k1RecoveryMethod2020 for verification and EIP-155 for account identification.

### 2. Ecosystem
**Very small.** Focused on the Real Items product ecosystem. Limited broader adoption.

### 3. Stability
**Moderate.** Simple specification, well-documented. The immutability design limits flexibility but ensures consistency.

## Recommendation
**No-go**

Requires Ethereum blockchain for all DID operations. Cannot function without access to the Ethereum network.
