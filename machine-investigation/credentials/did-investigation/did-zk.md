# did:zk (zCloak)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/zCloak-Network/zk-did-method-specs) |
| Organization | zCloak Network |
| DID Format | `did:zk:0x<40-hex-address>` or `did:zk:<chain>:<address>` |

## Overview
did:zk is a DID method using Arweave as the Verifiable Data Registry - a permanent storage blockchain ("global permanent hard drive"). The method supports both EVM-based (Ethereum-style addresses) and non-EVM chains (with chain prefix). DIDs and documents are stored permanently on Arweave. Notably, update and delete operations are NOT supported due to Arweave's immutable nature.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires Arweave blockchain:
- All DID documents stored permanently on Arweave
- Resolution via Arweave's tag-based querying system
- Create and Read supported; Update and Delete NOT supported
- Multi-chain address format (EVM and non-EVM)
- Controller must sign DID document before Arweave storage
- Supports Ed25519, Secp256k1, and X25519 cryptography

### 2. Ecosystem
zCloak Network focuses on zero-knowledge proof applications. The choice of Arweave provides permanent, censorship-resistant storage. Reference implementations: zk-did and zk-did-resolver repositories.

### 3. Stability
The immutability constraint (no updates/deletes) is significant. While providing permanence, this limits practical use cases where key rotation or DID revocation is needed. W3C DID Core compliant within these constraints.

## Recommendation
**No-go**

Requires Arweave blockchain infrastructure. The inability to update or delete DIDs is a significant limitation for most identity use cases requiring key rotation or revocation.
