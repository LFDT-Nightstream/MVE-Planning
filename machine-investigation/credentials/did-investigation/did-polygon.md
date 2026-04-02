# did:polygon (Polygon)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/ayanworks/polygon-did-method-spec) |
| Organization | AyanWorks / MATIC |
| DID Format | `did:polygon:[testnet:]<ethereum-address>` |

## Overview
did:polygon is a DID method that stores DID documents on the Polygon blockchain (formerly MATIC). It uses Ethereum key pairs as identities and relies on smart contracts deployed on Polygon Mainnet and Testnet for document storage and management.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires the Polygon blockchain for all operations:

- Smart contracts store and manage DID documents on-chain
- MATIC tokens required to pay gas fees for Create, Update, Delete operations
- Resolution requires querying the Polygon network
- Uses secp256k1 cryptography (Ethereum standard)

### 2. Ecosystem
**Medium-sized.** Benefits from Polygon's broader blockchain ecosystem. Reference implementations available (polygon-did-registrar, polygon-did-resolver). Universal Resolver integration exists.

### 3. Stability
**Moderate.** Follows W3C DID requirements. The specification is reasonably well-documented but tied to Polygon's blockchain infrastructure.

## Recommendation
**No-go**

Requires the Polygon blockchain for all DID operations. Cannot function without access to the Polygon network and MATIC tokens for transactions.
