# did:sideos (sideos)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/sideos/sideos-did-method) |
| Organization | sideos GmbH |
| DID Format | `did:sideos:[version:]<44-base58-chars>` |

## Overview
did:sideos is a DID method supporting cross-chain operations. DIDs are generated from P-256 (secp256r1) key pairs, with the identifier being a base58-encoded SHA-256 hash of the public key. Supports versioning for future cryptographic updates.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires ledger infrastructure:

- DIDs registered on a ledger identified by the version element
- Supports "cross-chain" operations - businesses select preferred ledgers
- Smart contracts or other means map DID to DID Document
- Resolution includes looking up documents and applying off-chain updates

Uses P-256/secp256r1 (different from typical secp256k1), SHA-256 hashing, Base58 encoding.

### 2. Ecosystem
**Small.** Focused on sideos platform. Limited public documentation and adoption evidence.

### 3. Stability
**Early to moderate.** Versioning system indicates planning for evolution. Documentation follows W3C DID Core specification.

## Recommendation
**No-go**

Requires ledger infrastructure for DID registration and resolution. The cross-chain design adds flexibility but still requires external blockchain/ledger access.
