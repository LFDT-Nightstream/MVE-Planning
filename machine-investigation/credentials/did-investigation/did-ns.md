# did:ns (Web 7.0 AgenticOS Decentralized Identifier Name System)

| Property | Value |
|----------|-------|
| Specification | [did-ns-1-0-1.md](https://github.com/Web7Foundation/Specifications/blob/main/methods/did-ns-1-0-1.md) |
| Organization | Web 7.0 Foundation (Canadian non-profit) |
| DID Format | `did:ns:<label>:<label>...<label>` (e.g., `did:ns:com:example:users:0e12c4ff-227b-4642-b37f-f1eff9d44914`) |

## Overview

The did:ns method maps DIDs to DNS infrastructure for resolution. It transforms colon-separated DID labels into DNS domain names, allowing DID documents to be retrieved via DNS TXT record lookups. For example, `did:ns:com:example:users:uuid` resolves to a DNS query for `uuid.users.example.com.did-ns.directory`.

The method supports three cryptographic algorithms (ECDSA, SM2, and EdDSA) and allows for hierarchical identifier structures that mirror DNS naming conventions. DIDs are registered through a runtime library function `RegIdWithPublicKey()`. The specification also supports alternative backends including Microsoft Active Directory and X.500/LDAP directories.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for on-ledger verification.**

Resolution requires DNS lookups to external infrastructure (specifically the `did-ns.directory` domain or similar DNS servers). Nodes would need to:
- Make external DNS queries to resolve any did:ns identifier
- Trust the DNS infrastructure and the Web 7.0 Foundation's directory service
- Handle network latency and potential DNS failures

This fundamentally conflicts with the requirement that nodes cannot make HTTP requests or external network calls to validate DIDs. There is no way to verify a did:ns identifier without querying external DNS infrastructure.

### 2. Ecosystem

**Limited ecosystem.**

- The specification is maintained by the Web 7.0 Foundation, which appears to be a relatively new organization
- Reference implementation exists at Web7Foundation/DnsServer on GitHub
- No evidence of widespread adoption or third-party tooling
- The "Web 7.0" branding and "AgenticOS" terminology suggest this is a niche/proprietary ecosystem
- No major wallet, framework, or platform support identified

### 3. Stability

**Early stage specification.**

- Currently at version 1.0.1
- Claims conformance to W3C DID v1.0 Proposed Recommendation
- Limited track record - difficult to assess long-term maintenance
- Single organization dependency (Web 7.0 Foundation)

## Special Considerations

- **DNS Dependency**: The entire resolution mechanism depends on the `did-ns.directory` domain being operational and accessible
- **Centralization Risk**: Despite being called "decentralized," resolution routes through a centralized DNS infrastructure controlled by the Web 7.0 Foundation
- **Deletion is Permanent**: Deleted DIDs cannot be reactivated, which could cause issues in recovery scenarios
- **No Query Operators**: The specification explicitly does not support query operators, limiting flexibility
- **Hierarchical Structure**: The colon-separated label format mirrors traditional DNS naming, which could be familiar to developers but also inherits DNS's hierarchical trust model

## Recommendation

**No-go**

The did:ns method is fundamentally incompatible with our key constraint that nodes cannot make HTTP requests or external network calls to validate DIDs. Resolution requires DNS lookups to external infrastructure (the `did-ns.directory` domain), making it impossible to verify these identifiers without network access.

Additionally, the limited ecosystem adoption, dependency on a single organization's infrastructure, and the relatively early stage of the specification make this an unsuitable choice even if the technical constraints could be addressed. The method introduces centralization through its DNS infrastructure despite the "decentralized" naming.
