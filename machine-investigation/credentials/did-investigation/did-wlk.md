# did:wlk (Weelink)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://weelink-team.github.io/weelink/DIDDesignEn) |
| Organization | Weelink |
| DID Format | `did:wlk:chNKtCNqYWLYWYW3gWRA1vnRykfCBZYHZvzKr` |

## Overview
did:wlk is a DID method for the Weelink blockchain, a new authentication system. The identifier is a Base58-encoded binary string with a "ch" prefix, derived from a 20-byte public key hash plus 4-byte checksum. The format encodes RoleType (account, node, device, etc.), KeyType (Ed25519, Secp256k1), and Hash algorithm (Keccak, SHA3 variants) in the first two bytes.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires the Weelink blockchain infrastructure:
- Resolution via gRPC requests to WLK network (RequestGetAccountState)
- DID operations via blockchain transactions (DeclareTx, UpdateTx, RevokeTx)
- Requires Weelink Wallet for DID creation and authentication
- BIP44 standard for hierarchical key derivation
- Supports historical version queries by block height

### 2. Ecosystem
Limited ecosystem. The Weelink-team maintains the project on GitHub. The method supports multiple role types (account, node, device, application, smart contract, bot, asset, stake, validator, group) suggesting enterprise/IoT use cases.

### 3. Stability
The specification claims full W3C DID compliance. Documentation available in English. The use of gRPC for resolution is somewhat unusual compared to REST APIs. BIP44 integration suggests cryptocurrency heritage.

## Recommendation
**No-go**

Requires the Weelink blockchain network and Weelink Wallet infrastructure. gRPC resolution requires access to WLK network nodes.
