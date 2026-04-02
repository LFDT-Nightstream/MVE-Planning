# did:fox (Fox DID Method)

| Property | Value |
|----------|-------|
| Specification | [Fox DID Method Specification](https://github.com/qikfox/fox-did-method/blob/main/README.md) |
| Organization | Qikfox (Authors: Smahi Maini, Tarun Gaur, Robert Wyatt Lutt, Hitesh Sevlia, Pawan Kumar) |
| DID Format | `did:fox:<base58-encoded-identifier>` (e.g., `did:fox:ALRFWtmnDFcAemPQxAKPvo`) |

## Overview

The did:fox method is a decentralized identifier system built on top of Trustnet, a permissioned distributed ledger based on Hyperledger Indy. The method-specific identifier is derived from an Ed25519 public key through SHA3-256 hashing, truncated to 16 bytes, and Base58-encoded.

Key features include:
- **Universal Handles**: Human-readable aliases for DIDs registered on-chain via QNS (a DNS-like system)
- **Privacy mechanisms**: Selective disclosure via BBS+ credentials, local encrypted key storage, and data minimization
- **Recovery**: 12-word mnemonic phrases for key recovery
- **Cryptographic support**: Ed25519, BBS+, and X25519 algorithms

CRUD operations are performed through authorized ledger gateway services using RESTful APIs with DIDComm v2 messaging.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for offline verification.**

The did:fox method requires access to the Trustnet ledger for all operations:
- **Resolution** requires either the Indy SDK or ledger gateway APIs to retrieve DID Documents
- **Creation/Update/Deactivation** requires authorization through permissioned ledger gateways
- DID Documents are stored on-chain, not derivable from the DID itself

Unlike self-certifying DID methods (e.g., did:key), the DID identifier alone does not contain sufficient information to verify signatures. Nodes would need to make HTTP calls to Trustnet infrastructure or run an Indy node to resolve DIDs.

### 2. Ecosystem

**Limited ecosystem.**

- The specification appears to be in early stages with minimal adoption data
- Tied specifically to the Qikfox/Trustnet ecosystem
- No evidence of widespread tooling, libraries, or community support outside of Qikfox
- No known implementations in major identity frameworks (e.g., Veramo, DIDKit)

### 3. Stability

**Early stage specification.**

- The specification is documented but appears to be specific to the Qikfox platform
- Dependent on Trustnet infrastructure availability and maintenance
- No version history or changelog indicating spec maturity
- Being a permissioned ledger, long-term availability depends on the operating organization

## Special Considerations

- **Permissioned nature**: Only authorized entities can write to the ledger, which provides some security guarantees but limits decentralization
- **Universal Handles**: The QNS system for human-readable aliases is an interesting UX feature but adds another layer of dependency
- **DID Deactivation**: DIDs cannot be truly deleted, only deactivated by removing verification methods and services
- **Hyperledger Indy foundation**: While Indy is a mature technology, it requires running specialized infrastructure

## Recommendation

**No-go**

The did:fox method cannot be supported in our context due to fundamental architectural incompatibility:

1. **External dependency required**: Resolving a did:fox DID requires access to the Trustnet ledger, either via HTTP calls to gateway services or by running Indy SDK/nodes. This violates our key constraint that nodes cannot make HTTP requests to validate DIDs.

2. **No self-certification**: Unlike did:key where the public key is embedded in the DID itself, did:fox DIDs are opaque identifiers that require ledger lookup to obtain the associated cryptographic material.

3. **Limited ecosystem**: Even if we could support external lookups, the limited adoption and tooling around did:fox would provide minimal value.

4. **Infrastructure dependency**: Supporting did:fox would require maintaining connectivity to a third-party permissioned network operated by Qikfox.
