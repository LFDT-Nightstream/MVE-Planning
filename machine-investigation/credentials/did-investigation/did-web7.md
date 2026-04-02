# did:web7 (Web 7.0 Ultraweb Decentralized Identifier Method)

| Property | Value |
|----------|-------|
| Specification | [did-web7-1-0-1.md](https://github.com/Web7Foundation/Specifications/blob/main/methods/did-web7-1-0-1.md) |
| Organization | Web 7.0 Foundation (Canadian non-profit, incorporated May 2023) |
| DID Format | `did:web7:<integer>` where integer is 0-32767 |

## Overview

The did:web7 method is designed for the Web 7.0 ecosystem, described as "a secure, message-based, decentralized operating system" built on the DIDComm Agent Architecture Reference Model (DIDComm-ARM). DIDs follow a simple format where the identifier is an integer between 0 and 32,767.

Key characteristics:
- **Resolution**: Uses a `GetDIDDocument()` function that queries Web 7.0 nodes
- **CRUD Operations**: Managed via abstract interface functions (`RegIdWithPublicKey()`, `AddKey()`, `RemoveKey()`)
- **Supported Algorithms**: ECDSA (secp224r1, secp256r1, secp384r1, secp521r1), SM2 (sm2p256v1), and EdDSA (ed25519)
- **Deactivation**: Permanently deactivates when all public keys are removed

The specification recommends querying "a sufficient number of nodes and compare each node's return value" for verification, indicating a distributed network dependency.

## Evaluation

### 1. Feasibility/Complexity

**Assessment: Not feasible for self-contained verification**

The did:web7 method has significant external dependencies:
- Resolution requires querying Web 7.0 network nodes via `GetDIDDocument()`
- The specification explicitly recommends querying multiple nodes to verify trustworthiness
- There is no mechanism to embed DID Document data or verification material within the DID itself
- The identifier is simply an integer (0-32767) that must be looked up in the Web 7.0 registry network

Nodes would need to make network calls to Web 7.0 infrastructure to resolve and verify these DIDs, which violates the constraint of no external HTTP/network dependencies.

### 2. Ecosystem

**Assessment: Very limited**

- The Web7 Foundation GitHub organization has only 6 followers and 18 repositories
- The Specifications repository has 1 star and 0 forks
- Primary implementation is in C# (Web7.TrustLibrary)
- No evidence of third-party implementations or widespread adoption
- No established tooling ecosystem outside the foundation's own projects
- The project appears to be primarily maintained by a single entity (Hyperonomy Digital Identity Lab)

### 3. Stability

**Assessment: Immature**

- Web 7.0 Foundation incorporated May 2023 (relatively new)
- Specification version 1.0.1 with only 29 commits to the specifications repository
- Limited community engagement (1 star, 0 forks)
- Security and privacy considerations are delegated to "platform implementors" rather than defined in the spec
- The DID method is tightly coupled to the Web 7.0 operating system ecosystem

## Special Considerations

- **Limited ID Space**: Only 32,768 possible DIDs (0-32767), which is extremely restrictive for any meaningful scale
- **Platform Lock-in**: The method is designed specifically for the Web 7.0 ecosystem and its "Trusted Personal Agents"
- **Abstract Interface**: The spec defines abstract CRUD operations but relies on platform-specific implementations
- **Network Dependency**: Resolution explicitly requires querying distributed Web 7.0 nodes

## Recommendation

**No-go**

The did:web7 method is not suitable for our use case for several critical reasons:

1. **External Network Dependency**: Resolution requires querying Web 7.0 network nodes. There is no way to verify a did:web7 DID without making network requests to the Web 7.0 infrastructure.

2. **Limited ID Space**: With only 32,768 possible identifiers, this method cannot scale to any meaningful number of users or entities.

3. **Immature Ecosystem**: The specification and tooling are extremely limited, with minimal community adoption and no third-party implementations.

4. **Platform Lock-in**: The method is tightly coupled to the Web 7.0 operating system and cannot be used independently.

5. **No Self-Contained Verification**: Unlike did:key or did:jwk, there is no way to derive verification material from the DID itself - it must always be looked up from the network.
