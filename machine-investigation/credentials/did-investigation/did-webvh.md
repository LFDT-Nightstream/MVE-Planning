# did:webvh (Web Verifiable History)

| Property | Value |
|----------|-------|
| Specification | [DIF](https://identity.foundation/didwebvh/v1.0/) |
| Organization | Decentralized Identity Foundation |
| DID Format | `did:webvh:<SCID>:<domain>[:<path>]` |

## Overview
did:webvh (Web Verifiable History) enhances did:web with verifiable history and cryptographic proof chains. The DID includes a Self-Certifying Identifier (SCID) - a 46-character base58-encoded hash of the initial log entry. DID documents are served as JSONL (JSON Lines) files containing the complete history. Each log entry includes Data Integrity proofs, version IDs, entry hashes, and timestamps forming an immutable chain.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry, but architecturally interesting.** Requires:
- TLS/SSL-secured web server hosting `did.jsonl`
- Valid domain name matching SSL certificate
- DNS resolution infrastructure
- Optional: witness servers for multi-signature approval
- Optional: watcher services for caching/monitoring

The cryptographic verification is self-contained once the document is retrieved. Controllers can publish both `did.json` (did:web) and `did.jsonl` (did:webvh) for backward compatibility.

### 2. Ecosystem
Strong ecosystem:
- Decentralized Identity Foundation governance
- Multiple implementations: TypeScript, Python, Go, Rust
- Version 1.0 represents stable release
- Uses same DID-to-HTTPS transformation as did:web

### 3. Stability
Version 1.0 is the stable release. Specification is well-documented with clear versioning. Cleanups and clarifications continue on Editor's Draft while maintaining semantic stability. DIF provides institutional backing.

## Recommendation
**Requires investigation**

While did:webvh requires web infrastructure for resolution, the cryptographic history chain provides valuable properties:
- Verifiable key rotation history
- Detection of DID forks/tampering
- Self-certifying identifier embedded in the DID

The verification logic is self-contained - if you have the JSONL file, you can verify the entire history cryptographically. Worth investigating whether the log verification could work with an alternative transport mechanism (e.g., ledger-based storage of the JSONL).
