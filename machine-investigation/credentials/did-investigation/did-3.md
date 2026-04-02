# did:3 (3ID Method)

| Property | Value |
|----------|-------|
| Specification | [CIP-79](https://cips.ceramic.network/CIPs/cip-79) |
| Organization | 3Box Labs |
| DID Format | `did:3:<ceramic-stream-id>` |

## Overview

The 3ID method is a decentralized identifier system built natively on the Ceramic Network. It uses Ceramic's Tile Document StreamType to create mutable streams that store DID document information, enabling key rotation through blockchain anchoring.

Example DID: `did:3:kjzl6cwe1jw149tlplc4bgnpn1v4uwk9rg9jkvijx0u0zmfa97t69dnqibqa2as`

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Create Tile Document with `did:key` controller, family `3id`, deterministic flag |
| Read | Load Ceramic stream, retrieve latest anchored commit, transform to DID document |
| Update | Modify stream content to add/remove public keys |
| Deactivate | Set content to `{"deactivated": true}` |

### Supported Algorithms

- secp256k1
- x25519
- Extensible via multicodec (post-quantum, BLS mentioned as future possibilities)

## Evaluation

### 1. Feasibility/Complexity

**High complexity / Not directly feasible**

The 3ID method is tightly coupled to the Ceramic Network infrastructure:

- **Ceramic Protocol Required**: DIDs are stored as Ceramic Tile Documents, which require a Ceramic node to create and resolve
- **IPFS Dependency**: Ceramic itself depends on IPFS for content addressing
- **Blockchain Anchoring**: Uses external blockchains (Ethereum mainnet historically) for timestamping and proof-of-publication
- **Stream-based Architecture**: The entire model assumes Ceramic's stream synchronization protocol

While the specification provides interesting ideas (mutable DIDs with key rotation, multicodec extensibility), these are not unique to did:3. The core functionality could be replicated without adopting the full Ceramic stack.

**Adaptation possibility**: Very low. Unlike some ledger-based methods where we could swap the underlying ledger, did:3 is fundamentally tied to Ceramic's data model and network topology. We would essentially need to run Ceramic infrastructure or rebuild its stream synchronization from scratch.

### 2. Ecosystem

**Declining / Niche**

- **3Box Labs pivot**: 3Box Labs, the creators, have pivoted focus to other products. The 3ID Connect wallet and related tooling have been deprecated
- **Ceramic adoption**: Ceramic itself has a small but dedicated community, primarily in the Web3/decentralized social space
- **Limited resolver support**: Not widely supported in mainstream DID resolver libraries compared to methods like did:key, did:web, or did:ethr
- **ComposeDB focus**: Ceramic's current focus is on ComposeDB for decentralized data, with 3ID being more of a legacy feature

### 3. Stability

**Uncertain**

- **Two versions exist**: v0 (legacy CID-based) and v1 (StreamID-based) - indicates breaking changes have occurred
- **Ceramic protocol evolution**: Ceramic itself is still evolving, which could impact did:3 resolution
- **CIP process**: Changes go through Ceramic Improvement Proposals, providing some governance structure
- **Last significant spec updates**: The specification appears stable but with limited recent activity

## Special Considerations

- **Not self-contained**: Unlike did:key or did:peer, resolving a did:3 requires network access to Ceramic nodes
- **Vendor lock-in risk**: Heavy dependency on 3Box Labs / Ceramic Network ecosystem
- **Interesting concepts**: The key rotation mechanism and multicodec extensibility are worth noting as design patterns, even if we don't adopt the method itself

## Recommendation

**No-go**

The did:3 method is too tightly coupled to Ceramic Network infrastructure to be practical for our registry. The ecosystem is declining with 3Box Labs having pivoted away from 3ID tooling. While the specification contains some interesting design patterns (mutable DIDs with key rotation), these can be achieved through other means without taking on the complexity and dependency of the full Ceramic stack.

If key rotation for DIDs is a desired feature, consider investigating:
- did:ion (Sidetree-based, ledger-agnostic potential)
- did:peer (numalgo 2 supports key rotation)
- did:webvh (web-based with version history)
