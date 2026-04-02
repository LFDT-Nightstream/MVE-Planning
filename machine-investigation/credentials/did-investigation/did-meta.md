# did:meta (Metadium)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/METADIUM/meta-DID/blob/master/doc/DID-method-metadium.md) |
| Organization | Metadium |
| DID Format | `did:meta:[network:]<MIN_64_hex>` |

## Overview
Metadium DID is a blockchain-based identifier method for the Metadium identity ecosystem. The identifier uses a 64-character hexadecimal Metadium Identifier Number (MIN). Resolution requires querying two smart contracts: Metadium Identity Manager (MIM) for management keys and Metadium Service Manager (MSM) for service keys.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** Resolution requires:
1. Query MIM smart contract for MANAGEMENT keys
2. Query MSM smart contract for SERVICE keys
3. Aggregate keys into DID Document

The method is fundamentally dependent on Metadium blockchain and deployed smart contracts (MIM compliant with EIP-1484).

### 2. Ecosystem
Metadium is a dedicated blockchain for decentralized identity with focus on Korean market. The ecosystem includes recovery mechanisms and provider key delegation for operational flexibility.

### 3. Stability
The specification follows W3C standards with full CRUD support. The use of EIP-1484 (a recognized Ethereum identity standard) provides some standardization. The dual-contract architecture (MIM/MSM) separates management and service concerns.

## Recommendation
**No-go**

Requires Metadium blockchain with MIM and MSM smart contracts. Cannot function without the underlying blockchain infrastructure.
