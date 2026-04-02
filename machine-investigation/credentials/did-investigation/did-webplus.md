# did:webplus (Web Plus)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://ledgerdomain.github.io/did-webplus-spec/) |
| Organization | LedgerDomain (Victor Dods) |
| DID Format | `did:webplus:example.com:uHiBKHZUE3HHlYcyVIF-vPm0Xg71vqJla2L1OGXHMSK4NEA` |

## Overview
did:webplus extends did:web with cryptographic verifiability of a DID's entire history. The DID includes a self-certifying identifier (SCID) - a hash of the root DID document. DID documents are served as JSONL (JSON Lines) files containing the complete cryptographically-linked history. Each document links to its predecessor via prevDIDDocumentSelfHash, forming an immutable chain. Designed for regulated ecosystems like pharmaceutical supply chains.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires external web infrastructure:
- Resolution via HTTPS to `did-documents.jsonl` endpoint
- Requires web server (Verifiable Data Registry - VDR) to host documents
- Uses DNS for domain resolution
- Optional Verifiable Data Gateway (VDG) for large-scale caching/archiving
- HTTP Range-Based GET for incremental updates
- JWS proofs with detached-payload signatures

However, the cryptographic chain provides verifiable history independent of the server, which is a significant improvement over did:web.

### 2. Ecosystem
Emerging method with interesting properties. LedgerDomain focuses on regulated supply chains. The specification includes resolver variants (Full and Thin) for different deployment scenarios. JSON Canonicalization Scheme (JCS) compliance required.

### 3. Stability
Well-documented specification. Addresses did:web's key rotation and history verification weaknesses. The cryptographic proof chain enables detection of DID forks (split-brain scenarios).

## Recommendation
**No-go**

Requires HTTPS/DNS infrastructure for resolution, similar to did:web. While it adds valuable cryptographic verifiability, it cannot function without external web infrastructure.
