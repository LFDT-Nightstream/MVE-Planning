# did:jwks (JWKS DID Method)

| Property | Value |
|----------|-------|
| Specification | [SPEC.md](https://github.com/catena-labs/did-jwks/blob/main/SPEC.md) |
| Organization | Catena Labs, Inc. (Matt Venables) |
| DID Format | `did:jwks:{domain-name}[:{path-segment}]*` |

## Overview

The did:jwks method bridges existing OAuth2/OIDC infrastructure with the DID ecosystem by transforming JWKS (JSON Web Key Set) endpoints into DID identifiers. It allows any domain with a published JWKS endpoint to be represented as a DID.

The method works through a two-step discovery process:
1. Attempts to fetch JWKS directly from `https://{domain}{/path}/.well-known/jwks.json`
2. Falls back to OAuth2 discovery by fetching `https://{domain}{/path}/.well-known/openid-configuration` and extracting the `jwks_uri`

DID documents are generated dynamically by converting JWKS keys into verification methods. Each key becomes a verification method with RFC 7638 JWK thumbprints as fragment identifiers, automatically assigned to `assertionMethod`/`authentication` (for signing keys) or `keyAgreement` (for encryption keys).

Example: `did:jwks:accounts.google.com` resolves to Google's JWKS endpoint and generates a DID document from the published keys.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case.**

The did:jwks method fundamentally requires HTTP calls to resolve DIDs. The entire method is predicated on fetching JWKS endpoints from remote servers:
- Resolution requires network access to JWKS endpoints
- There is no self-contained or offline resolution mechanism
- The DID document is generated dynamically from the fetched JWKS content
- Key rotation at the source endpoint changes the resolved DID document

Since nodes cannot make HTTP requests to validate DIDs, this method cannot be supported without external dependencies.

### 2. Ecosystem

**Limited adoption.**

- Repository created July 2025 with minimal activity (13 commits, 4 stars, 2 forks)
- Two TypeScript implementations available: `did-jwks` CLI and `jwks-did-resolver` plugin
- Submitted to W3C DID Extensions Registry but not widely adopted
- Primary value proposition is integration with existing OAuth2/OIDC infrastructure

### 3. Stability

**Early stage specification.**

- Relatively new specification (created mid-2025)
- No explicit versioning in the specification
- Active but limited development
- Maintained by a small team at Catena Labs

## Special Considerations

- **Security bound to DNS/TLS**: The security model depends entirely on DNS and TLS infrastructure security
- **No historical verification**: Rotated keys cannot verify signatures created with previously valid keys, limiting use to short-lived tokens
- **No customization**: Generated DID documents cannot include service endpoints, custom controllers, or additional metadata
- **Privacy exposure**: Resolution activity is visible to endpoint operators since it requires fetching from their servers

## Recommendation

**No-go**

The did:jwks method is fundamentally incompatible with our constraint that nodes cannot make HTTP requests to validate DIDs. The entire resolution process requires fetching JWKS endpoints from remote servers at resolution time. There is no self-contained representation of the DID document - it must always be derived from the current state of the remote JWKS endpoint.

Additionally, the method's design around dynamic key rotation means that even if we could cache resolved documents, they would become stale and potentially invalid as the source endpoint rotates keys. This method is specifically designed for real-time verification scenarios with network access, not for offline or ledger-based verification.
