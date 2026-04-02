# did:jlinc (JLINC Protocol)

| Property | Value |
|----------|-------|
| Specification | [JLINC DID Spec](https://did-spec.jlinc.org/) |
| Organization | Portable Data Corporation |
| DID Format | `did:jlinc:<host>:<id-string>` |

## Overview
JLINC DID is a method built on the JLINC Protocol for data rights and personal data exchange. The identifier is a hash of the shortName, public key, and recovery hash. Resolution occurs through a federated network using the ActivityPub protocol rather than blockchain.

## Evaluation

### 1. Feasibility/Complexity
**Requires external HTTP infrastructure.** The method depends on:
- Federated resolver network supporting ActivityPub protocol
- HTTP GET requests to resolver endpoints
- Ed25519 cryptography (mandatory for version 2.0.0)
- SHA256 hashing for recovery key operations

Resolution requires network connectivity to federated resolvers. The method supports key rotation and recovery through a pre-committed recovery key hash.

### 2. Ecosystem
Niche ecosystem focused on data rights management. Influenced by ActivityPub community, did:web, and KERI projects. Limited adoption outside specific data sovereignty use cases.

### 3. Stability
The specification follows "convention over configuration" principles, standardizing cryptographic methods in the version spec rather than each document. This reduces document size but creates version dependencies.

## Recommendation
**No-go**

Requires federated HTTP resolver network using ActivityPub protocol. Cannot function without external network infrastructure for resolution.
