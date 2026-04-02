# did:tyron (Tyron/Zilliqa)

| Property | Value |
|----------|-------|
| Specification | [tyronzil.com](https://www.tyronzil.com) |
| Organization | Julio Cabrapan Duarte |
| DID Format | `did:tyron:zil:[network]:<contract-address>` |

## Overview
did:tyron (tyronzil) is the first DID method for the Zilliqa blockchain, funded by ZILHive Innovation grants. It uses Scilla smart contracts for identity management. Scilla is designed for safety with formal verification support via Coq proof assistant.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Zilliqa blockchain:

- Implemented via did.tyron smart contract in Scilla
- DID format example: `did:tyron:zil:test:0x5a156a1d18a9a76a0a86b62fcdcb2e547173f3c9`
- Uses SchnorrSecp256k1VerificationKey2019 (Zilliqa's signature scheme)
- Public keys in Base58 Bitcoin encoding
- xWALLET DApps for SSI account management

### 2. Ecosystem
**Small.** First mover in Zilliqa SSI space. TyronDEX integration for DeFi. Limited to Zilliqa ecosystem.

### 3. Stability
**Moderate.** Scilla's formal verification provides security guarantees. Depends on Zilliqa platform stability.

## Recommendation
**No-go**

Requires Zilliqa blockchain for all DID operations. Cannot function without access to Zilliqa network and Scilla smart contract execution.
