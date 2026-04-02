# did:nuts (Nuts Distributed Registry)

| Property | Value |
|----------|-------|
| Specification | [RFC006 Distributed Registry](https://nuts-foundation.gitbook.io/drafts/rfc/rfc006-distributed-registry) |
| Organization | Nuts Foundation (Netherlands) |
| DID Format | `did:nuts:<base58-encoded-sha256-of-jwk>` |

## Overview

The did:nuts method is a decentralized identity system designed specifically for healthcare data exchange in the Netherlands. It operates on a mesh network without central authority, where DID documents are distributed across network participants using a Verifiable Transactional Graph (RFC004) and gRPC-based network communication (RFC005).

The DID identifier is derived from a public key using the formula: `idstring = BASE-58(SHA-256(JWK))`, following RFC7638 specifications. This design prevents DID hijacking since the identifier is cryptographically bound to the original key pair. DID documents are wrapped in JSON Web Signatures (JWS) to ensure cryptographic authenticity and integrity across the distributed network.

Key characteristics:
- DIDs can only be resolved locally from the node's stored state
- All nodes maintain a local copy of DID documents received through the Nuts Network
- Uses CRDT-compatible conflict resolution for distributed state management
- Supports only elliptic curve cryptography (secp256r1, secp384r1, secp521r1) with JsonWebKey2020 format
- Two-way TLS required for all network communications

## Evaluation

### 1. Feasibility/Complexity

**Not feasible without external dependencies.**

The did:nuts method fundamentally requires participation in the Nuts network infrastructure. Key barriers:

- **Network Dependency**: DID resolution requires local state synchronized from the Nuts mesh network. A node cannot verify a did:nuts DID without either participating in the network or having received the DID document through out-of-band means.
- **Custom Protocol Stack**: Requires implementation of RFC004 (Verifiable Transactional Graph) and RFC005 (Distributed Network using gRPC) protocols.
- **State Synchronization**: The "Verifiable Data Registry" concept requires receiving and processing Create, Update, and Delete operations from network participants.

While the DID format itself is self-certifying (derived from a public key hash), the DID document containing verification methods, service endpoints, and other metadata cannot be reconstructed from the DID alone. Verification requires access to the full DID document from the network.

### 2. Ecosystem

**Limited ecosystem, healthcare-focused.**

- **Reference Implementation**: [nuts-node](https://github.com/nuts-foundation/nuts-node) written in Go (27 stars, 20 forks, 2,637 commits)
- **Admin Tool**: [nuts-admin](https://github.com/nuts-foundation/nuts-admin) for identity management
- **Community**: Active Slack community, primarily Netherlands-based healthcare organizations
- **Adoption**: Focused on Dutch healthcare sector (care organizations, software vendors, SaaS providers)
- **Standards**: Integrates with OpenID4VC, PEX, and did:web for broader interoperability
- **License**: GPL-3.0

The ecosystem is active but niche, with development continuing as of late 2025. However, adoption outside the Dutch healthcare sector appears minimal.

### 3. Stability

**Stable specification, actively maintained.**

- **Spec Status**: RFC006, dated January 2021
- **Maturity**: Production-ready with strict mode for production environments
- **Maintenance**: Active development with regular commits to nuts-node
- **Dependencies**: Relies on multiple Nuts Foundation RFCs (RFC004, RFC005)
- **Cryptography Note**: Specification acknowledges algorithm support may expand as library support improves

The specification is reasonably mature for its intended use case, but it is tightly coupled with the broader Nuts Foundation protocol stack.

## Special Considerations

- **Healthcare Focus**: Designed specifically for healthcare data exchange with compliance considerations for that domain
- **Local Resolution Only**: The spec explicitly states "A Nuts DID can only be resolved locally" - there is no universal resolver
- **Historical Queries**: Supports querying historic versions of DID documents with timestamps
- **Controller Model**: DIDs can have controllers (other DIDs that can manage them), adding complexity to verification
- **Geographic Scope**: Primarily designed for and adopted in the Netherlands healthcare system

## Recommendation

**No-go**

The did:nuts method cannot be supported without participating in the Nuts network infrastructure. The fundamental architecture requires:

1. Joining a mesh network with gRPC communication and two-way TLS
2. Synchronizing state through the Verifiable Transactional Graph protocol
3. Maintaining local storage of all DID documents from network participants

This violates the key constraint that nodes cannot make network requests to validate DIDs. Unlike self-describing methods like did:key or did:jwk where the DID document can be derived from the identifier alone, did:nuts requires external network participation to obtain DID documents. There is no way to verify a did:nuts DID in isolation without access to the distributed registry state.
