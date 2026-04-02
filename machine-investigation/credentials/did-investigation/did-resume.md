# did:resume (Resume DID)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/realmatterio/Resume-DID-Method/) |
| Organization | RealMatter (Ming-lam Ng) |
| DID Format | `did:resume:<ledger>:<40-hex-chars>` |

## Overview
did:resume is a DID method for resume/credential management. The subject identifier is derived from a hardware-generated public key, with the first 20 hex digits encoded in base16 multibase format. Designed for secure credential storage using hardware security modules.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method has multiple external dependencies:

- References "verifiable distributed ledger" as backing infrastructure
- Resolution via HTTP REST endpoints at `did-resume.web.app`
- Requires hardware security modules for key storage
- OAuth-style token authentication for operations
- Path-based queries for credential types (/bio, /cert, /qual, /exp)

### 2. Ecosystem
**Very small.** Appears to be a single-organization project. Limited adoption and community activity.

### 3. Stability
**Early stage.** Focused on resume/credential use case. Documentation indicates active development.

## Recommendation
**No-go**

Requires external HTTP endpoints and potentially distributed ledger infrastructure. The dependency on `did-resume.web.app` service makes it unsuitable for self-contained operation.
