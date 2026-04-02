# did:jlinx (JLINX Protocol)

| Property | Value |
|----------|-------|
| Specification | [JLINX DID Spec](https://didspec.jlinx.io) |
| Organization | Portable Data Corporation |
| DID Format | `did:jlinc:<host>:<id-string>` |

## Overview
JLINX DID (also using the jlinc method name) is an evolution of the JLINC DID method. The identifier is formed from a hash of the active controller public key, active signing public key, and a hashed "next" controller public key for key rotation support.

## Evaluation

### 1. Feasibility/Complexity
**Requires external HTTP/DNS infrastructure.** For HTTPS-based DIDs:
- The `h:` prefix transforms to `https://`
- Subsequent colons become path separators for URL formation
- HTTP endpoints store and serve DID documents

The specification notes that relying parties can resolve DIDs without a universal resolver, but still requires HTTP endpoint availability. Optional IPFS support for content-addressed storage.

### 2. Ecosystem
Similar to did:jlinc, focused on data rights and personal data management. The "next" key mechanism for key rotation is a notable feature for continuity.

### 3. Stability
The specification emphasizes minimalism through convention. Supports inception, update, and revocation operations. Uses JWS (signed JSON Web Tokens) for agent-based document transmission.

## Recommendation
**No-go**

Requires HTTP/HTTPS endpoints for document storage and retrieval. Cannot function in a self-contained manner without network infrastructure.
