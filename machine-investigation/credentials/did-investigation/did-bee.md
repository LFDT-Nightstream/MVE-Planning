# did:bee (Sustainability/Regenerative Ag DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:bee Method](https://github.com/mesur-io/did-method-bee) |
| Organization | mesur.io |
| DID Format | `did:bee:<uuid>[:<fqdn>]` |

## Overview

The did:bee method is designed for sustainability and regenerative agriculture applications. It uses a centralized platform (Earthstream) for DID management with OAuth-based access control.

Example DIDs:
- `did:bee:990aa5bb-eaa4-43cd-9f51-bcd4f6f2576a` (default environment)
- `did:bee:e304a73f-c044-475b-860f-7d7b8e9dea4a:bee-test.mesur.io` (test environment)
- `did:1F41D:b54399c1-fbec-4aa8-807f-9b7ee057adb0` (emoji variant)

### DID Structure

- **Prefix**: `did:bee` or `did:1F41D` (hex-encoded bee emoji)
- **Identifier**: v4 UUID
- **Environment** (optional): FQDN (defaults to `bee.mesur.io`)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | HTTP POST to `bee.mesur.io/did/{did}` (OAuth scope: `create:dids`) |
| Read | HTTP GET (OAuth scope: `resolve:dids`) |
| Update | HTTP PUT (OAuth scope: `update:dids`) |
| Delete | HTTP DELETE (OAuth scope: `delete:dids`) |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bee method is entirely centralized:

- **Centralized platform**: All operations go through `bee.mesur.io` (Earthstream platform)
- **OAuth-based access**: Requires OAuth scopes for all operations
- **No blockchain**: Despite being a DID method, uses traditional centralized infrastructure
- **UUID-based**: Simple UUID identifiers with no cryptographic derivation

**Adaptation possibility**: None. This is essentially a centralized identity service wrapped in DID syntax. There's no decentralized verification possible.

### 2. Ecosystem

**Very limited / Domain-specific**

- **Single organization**: mesur.io proprietary system
- **Specific use case**: Sustainability and regenerative agriculture
- **No Universal Resolver support**: Proprietary resolution only
- **Future DWN plans**: Mentions potential Decentralized Web Nodes integration

### 3. Stability

**Early / Evolving**

- **Specification notes**: "URL(s) listed may change in future versions"
- **Single maintainer**: mesur.io controls all infrastructure
- **Limited documentation**: Basic specification only

## Special Considerations

- **Privacy focused**: Prohibits PII in DID documents
- **HSM requirement**: Mandates Hardware Security Modules for key management
- **W3C Traceability**: References W3C Traceability Interoperability spec

## Recommendation

**No-go**

The did:bee method is unsuitable for our use case:

1. **Centralized**: All operations depend on mesur.io infrastructure
2. **OAuth gated**: Requires authentication with their platform
3. **Domain-specific**: Designed for sustainability/agriculture, not general identity
4. **No decentralization**: Despite DID branding, this is a centralized system

This method provides no benefits over traditional centralized identity systems and would introduce vendor lock-in.
