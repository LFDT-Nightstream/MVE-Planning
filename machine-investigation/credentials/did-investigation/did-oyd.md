# did:oyd (OwnYourData DID Method)

| Property | Value |
|----------|-------|
| Specification | [OwnYourData DID Method Specification](https://ownyourdata.github.io/oydid/) |
| Organization | OwnYourData (Austria) - Editors: Christoph Fabianek, Wolfgang Kampichler |
| DID Format | `did:oyd:[multibase-encoded-multihash]` or `did:oyd:[identifier]@[host]` |

## Overview

The did:oyd method is a self-sovereign identity approach that derives DID identifiers cryptographically from the DID document content, cryptographic keys, and provenance logs. Unlike blockchain-based methods, it stores DID documents in pluggable repositories (HTTP servers or local files) while maintaining integrity through hash-linked directed acyclic graphs (DAGs).

Key characteristics:
- Uses two ED25519 key pairs: a document key (for updates) and a revocation key (for deactivation)
- DID identifier is a Multibase (base58-btc) encoded Multihash (sha2-256) of the document payload and keys
- Resolution involves traversing a DAG of log entries to find the current unrevoked DID document
- Updates create new versions linked cryptographically to previous versions via signatures
- Supports optional location hints via `@host` suffix in the DID

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case.**

The did:oyd method fundamentally relies on external repository access for DID resolution:
- DID documents are stored in HTTP repositories (default: `https://oydid.ownyourdata.eu`)
- Resolution requires querying REST API endpoints (`/log/{:id}`) to retrieve log entries
- The DAG traversal needed to find the current DID document state requires multiple HTTP calls
- Even with the `@host` location hint, resolution still requires network access to that host
- Local file storage option exists but is designed for the DID owner, not for third-party verification

While the method avoids blockchain dependencies, it replaces them with HTTP repository dependencies. Nodes would need to make external HTTP calls to resolve and verify DIDs, which violates our core constraint.

### 2. Ecosystem

**Limited ecosystem:**
- Maintained by a small Austrian organization (OwnYourData)
- Reference implementations exist in Ruby
- Primarily designed for "local settings with a limited number of stakeholders"
- No evidence of widespread adoption or major integrations
- Community-driven development via GitHub

### 3. Stability

**Moderate stability:**
- Current status: Draft Community Group Report (not a W3C Standard)
- Version 0.5 released May 30, 2023
- Last specification update: February 21, 2024
- Active maintenance but limited community size
- Cryptographic approach is sound (Ed25519, SHA-256, RFC 8785 JSON canonicalization)

## Special Considerations

**Interesting design aspects:**
- Content-addressable: DID identifier is derived from document content, providing inherent integrity verification
- Supports offline DID creation (though resolution still requires repository access)
- Allows DID "cloning" for redundancy across multiple repositories
- Designed to avoid costly blockchain infrastructure while maintaining cryptographic guarantees
- Supports key rotation through the DAG-based log chain

**Limitations:**
- Repository availability is critical - maintainers must ensure repositories are online
- No decentralized consensus mechanism
- Currently limited to ED25519 (though architecture is extensible)
- Resolution complexity increases with document update history

## Recommendation

**No-go**

The did:oyd method cannot be supported without external dependencies. Despite avoiding blockchain requirements, it fundamentally relies on HTTP access to repository servers for DID resolution. The resolution process requires:

1. Querying one or more repositories to retrieve log entries
2. Traversing a DAG structure across potentially multiple HTTP requests
3. Verifying the chain of signatures and hashes to find the current document state

There is no way to verify a did:oyd DID document without network access to the repository where it is stored. The self-contained verification we require (where all necessary data can be provided inline without external lookups) is not supported by this method's architecture.

Even if a DID document were provided directly, verification would require access to the complete log history, which is only available from the repository. This makes did:oyd unsuitable for environments where nodes cannot make HTTP requests to validate DIDs.
