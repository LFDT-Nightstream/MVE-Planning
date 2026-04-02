# did:wba (Web-Based Agent)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/chgaowei/AgentNetworkProtocol/blob/main/03-did%3Awba%20Method%20Design%20Specification.md) |
| Organization | Gaowei Chang |
| DID Format | `did:wba:example.com` or `did:wba:example.com:user:alice` |

## Overview
did:wba is a web-based DID method that uses fully qualified domain names as identifiers, similar to did:web. The method uses TLS/SSL certificate-protected domains with optional path components (using colons instead of slashes). Resolution involves constructing HTTPS URLs and fetching DID documents from well-known locations. Port numbers require percent-encoding.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires external web/DNS infrastructure:
- Domain name with valid TLS/SSL certificate required
- Resolution via HTTPS requests to the domain
- DID documents stored at `/.well-known/did.json` or path-based URLs
- Requires clients to verify CA certificate trustworthiness
- DNS infrastructure for domain resolution
- Web server to host DID documents

This is essentially a variant of did:web with similar infrastructure requirements.

### 2. Ecosystem
New/emerging method. Part of the AgentNetworkProtocol project. Designed for AI agent identity (hence "Web-Based Agent"). Single author specification suggests early-stage development.

### 3. Stability
Specification is in a GitHub repository as part of a broader agent network protocol. Early-stage development. Similar security model to did:web - relies on DNS and CA infrastructure.

## Recommendation
**No-go**

Requires external DNS, TLS/SSL certificates, and web hosting infrastructure. Functionally similar to did:web with the same HTTP/DNS resolution dependencies.
