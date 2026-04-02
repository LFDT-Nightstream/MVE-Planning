# did:web (Web DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:web Spec](https://w3c-ccg.github.io/did-method-web/) |
| Organization | W3C Credentials Community Group |
| DID Format | `did:web:<domain>[:path]` |

## Overview

The did:web method resolves DIDs via HTTPS by fetching a DID Document from a well-known location on a web server. It leverages existing web infrastructure (DNS, TLS) for identity management.

Example DIDs:
- `did:web:example.com` → `https://example.com/.well-known/did.json`
- `did:web:example.com:user:alice` → `https://example.com/user/alice/did.json`
- `did:web:example.com%3A8080` → `https://example.com:8080/.well-known/did.json`

### DID Structure

- **Prefix**: `did:web:`
- **Domain**: Fully qualified domain name (FQDN)
- **Path** (optional): Colon-delimited path components
- **Port** (optional): Percent-encoded colon (`:` → `%3A`)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Register domain, configure web server, create `did.json` file |
| Read | Construct HTTPS URL, fetch `did.json`, validate document |
| Update | Update `did.json` file on server |
| Deactivate | Remove or make `did.json` unavailable |

### Resolution Process

1. Replace colons with forward slashes
2. Percent-decode port colons
3. Prepend `https://`
4. Append `/.well-known` if no path
5. Append `/did.json`
6. Execute HTTPS GET request
7. Verify document ID matches requested DID

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for on-ledger verification**

The did:web method requires HTTP requests for resolution:

- **HTTP required**: Nodes would need to make external HTTPS requests
- **DNS dependency**: Resolution depends on DNS infrastructure
- **TLS verification**: Requires certificate validation
- **Cannot verify on-chain**: Ledger nodes can't make HTTP calls

**Adaptation possibility**: Very low for on-chain verification. However, there are alternatives:

1. **Client-side resolution**: Users resolve did:web off-chain and provide proof
2. **zkTLS proofs**: Zero-knowledge proofs of TLS sessions (complex, emerging tech)
3. **Oracle pattern**: Trusted oracle provides did:web resolution (centralization risk)

### 2. Ecosystem

**Excellent / Widely used**

- **W3C CCG**: Official specification
- **Wide adoption**: Used by many organizations (Bluesky, enterprises)
- **Simple infrastructure**: Uses existing web servers
- **Universal Resolver**: Full support
- **Production use**: Real-world deployments at scale

### 3. Stability

**Stable**

- **Mature specification**: Well-documented
- **Follows web standards**: DNS, TLS, HTTPS
- **Active maintenance**: W3C CCG oversight
- **Conservative design**: Leverages proven web infrastructure

## Special Considerations

### Security Concerns

- **DNS dependency**: DNS hijacking could redirect to malicious documents
- **DNSSEC recommended**: RFC4033/4034/4035 for DNS integrity
- **TLS requirements**: NIST SP 800-52 Rev. 2 compliance
- **No built-in authentication**: Document updates rely on web server access control

### Privacy Concerns

- **DNS tracking**: DNS providers can track all resolutions
- **Server tracking**: Web servers log document retrievals
- **Correlation risk**: Easy to correlate did:web usage

### Advantages

- **Familiar infrastructure**: Web servers, domains, TLS
- **Easy to set up**: No blockchain or special infrastructure
- **Human-readable**: Domain names are meaningful
- **Enterprise-friendly**: Aligns with existing web identity

## Recommendation

**No-go for on-ledger verification, but consider for ecosystem compatibility**

The did:web method cannot be directly verified on our ledger because:

1. **HTTP dependency**: Ledger nodes cannot make HTTP requests
2. **DNS resolution**: Requires external DNS infrastructure
3. **Dynamic nature**: Document can change without on-chain record

**Possible partial support:**
- Document that did:web cannot be verified on-chain
- Provide client-side did:web resolution in SDKs
- Consider allowing did:web in metadata with caveat that verification is client-side only

**Alternative approach:**
If users need domain-based identity, consider:
- did:webvh (web with version history) - has on-chain anchoring
- did:dns - DNS-based but potentially more verifiable

For our core registry functionality, did:key and similar self-verifying methods are more appropriate. did:web is mentioned in README.md as an example of methods we cannot support "in a trustless manner" without additional protocols like zkTLS.
