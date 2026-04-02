# did:uns (UNS Network)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/unik-name/did-method-spec/blob/main/did-uns/UNS-DID-Specification.md) |
| Organization | Space Elephant SAS (France) |
| DID Format | `did:uns:UYWaMkArHJjMecuHgs6LYapFtvV27QeafX` |

## Overview
did:uns is a DID method for the uns.network blockchain, which is built on ARK.io technology. The method uses 34-character Base58-encoded public keys as identifiers. DIDs are immutable once created (no update or delete operations). The method supports different network prefixes: "U" for Livenet and "S" for Sandbox.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Entirely dependent on uns.network blockchain:
- Resolution requires querying uns.network blockchain
- Uses ARK.io cryptographic infrastructure
- Requires BIP39 passphrases and Secp256k1 ECDSA signing
- Network-specific prefixes tie DIDs to particular environments
- DIDs are immutable (no updates/deletions supported)

### 2. Ecosystem
Very small ecosystem. Companion to did:unik, both maintained by the same French company. Limited adoption outside the Unikname/uns.network platform.

### 3. Stability
Registered with W3C DID Specification Registries. The immutability constraint (no updates/deletions) is a significant limitation for real-world use cases.

## Recommendation
**No-go**

Requires uns.network (ARK.io-based) blockchain infrastructure. Cannot function independently.
