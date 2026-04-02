# did:dns (DNS DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:dns Spec](https://danubetech.github.io/did-method-dns/) |
| Organization | Danube Tech (Markus Sabadello) |
| DID Format | `did:dns:<fully-qualified-domain-name>` |

## Overview

The did:dns method uses DNS infrastructure for decentralized identifiers, storing verification methods and services in DNS resource records. Unlike did:web, it requires only DNS infrastructure, not web servers.

Example DIDs:
- `did:dns:danubetech.com`
- `did:dns:support.examplecompany.com`

### DID Structure

- **Prefix**: `did:dns:`
- **Identifier**: Valid fully-qualified domain name (RFC compliant)

### Resolution Process

1. Query DNS for URI-type resource records
2. Pattern: `_<id>._did.<domain>`
3. Extract verification methods as did:key references
4. Construct DID document

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Register domain, configure DNS zone with resource records |
| Read | Query DNS for URI-type RRs matching pattern |
| Update | Modify DNS zone file entries |
| Deactivate | Delete domain (with DNS caching considerations) |

### Dependencies

- DNS infrastructure
- DNSSEC (strongly recommended)
- did:key method for embedded verification methods

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dns method has similar issues to did:web:

- **DNS resolution required**: Cannot verify from ledger without HTTP/DNS
- **External infrastructure**: Depends on DNS system
- **Caching issues**: DNS TTL affects deactivation timing
- **Domain recycling**: Security concerns with domain expiration

**Adaptation possibility**: None. DNS resolution cannot be performed from our ledger nodes without external queries.

### 2. Ecosystem

**Niche but credible**

- **Danube Tech**: Reputable identity company
- **Markus Sabadello**: Well-known in DID community
- **DIF Universal Resolver**: Expected support
- **Alternative to did:web**: Specific technical advantages

### 3. Stability

**Draft but maintained**

- **Unofficial draft**: W3C CCG specification
- **Active author**: Markus Sabadello maintains
- **DNS stability**: Relies on stable DNS infrastructure

## Special Considerations

### Advantages over did:web

- No web server required
- Only DNS infrastructure needed
- Simpler operational requirements

### Limitations

- Same trust model as DNS (CA/DNSSEC)
- Domain control = identity control
- Cannot be verified on-ledger

## Recommendation

**No-go**

The did:dns method is unsuitable for our use case:

1. **DNS dependency**: Requires external DNS resolution
2. **Same issue as did:web**: Cannot verify from ledger nodes
3. **Domain control**: Identity tied to domain ownership
4. **No on-ledger verification**: External queries required

While simpler than did:web (no web server needed), it shares the fundamental limitation that we cannot verify DNS from our ledger.
