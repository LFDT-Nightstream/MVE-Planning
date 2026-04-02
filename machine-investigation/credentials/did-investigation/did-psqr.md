# did:psqr (Public Square)

| Property | Value |
|----------|-------|
| Specification | [VPSQR](https://vpsqr.com/did-method-psqr/v1/) |
| Organization | Christian Gribneau / VPSQR |
| DID Format | `did:psqr:<domain>[/path]` |

## Overview
did:psqr is a DID method for "Virtual Public Squares" that uses web hosts identified by domain names. DIDs are essentially HTTPS URLs with the protocol replaced by the did:psqr prefix. Documents are served as static JSON files or via web applications.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires DNS and HTTPS infrastructure:

- Resolution converts DID to HTTPS URL and fetches document
- Requires TLS/SSL certificate matching the domain
- Documents hosted at `/.well-known/psqr` or custom paths
- Must be served with application/json or application/did+json media types

Essentially a variant of did:web tied to domain name ownership.

### 2. Ecosystem
**Small.** Used within the Virtual Public Squares project ecosystem. npm packages available (psqr-did-resolver). WordPress plugin exists for serving DIDs.

### 3. Stability
**Moderate.** Specification appears complete but the certificate for the specification URL has expired, raising concerns about project maintenance.

## Recommendation
**No-go**

Requires DNS resolution and HTTPS infrastructure for DID resolution. The fundamental dependency on web hosting makes it unsuitable for self-contained operation.
