# did:jwk (JSON Web Key DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:jwk Spec](https://github.com/quartzjer/did-jwk/blob/main/spec.md) |
| Organization | Community |
| DID Format | `did:jwk:<base64url-encoded-jwk>` |

## Overview

The did:jwk method encodes a JSON Web Key (JWK) directly into the DID identifier. Like did:key, it's a self-contained, deterministic method that requires no registry. The key difference is that did:jwk uses the widely-adopted JWK format (RFC 7517) rather than multicodec encoding.

Example DID: `did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6IjB...`

### DID Structure

- **Prefix**: `did:jwk:`
- **Identifier**: Base64url-encoded JWK (RFC 4648, RFC 7517)

### Generation Process

1. Generate or load a JWK
2. Serialize to UTF-8 JSON string
3. Encode using base64url
4. Prepend `did:jwk:`

### DID Document Template

Generated document includes:
- `@context` with W3C DID and JWS 2020 contexts
- `verificationMethod` with JsonWebKey2020 type
- `assertionMethod`, `authentication`, `capabilityInvocation`, `capabilityDelegation`
- `keyAgreement` (conditional based on `use` property)

### Supported Key Types

- **P-256**: ECDSA signatures
- **X25519**: Key agreement/encryption
- Other JWK-compatible key types

## Evaluation

### 1. Feasibility/Complexity

**Highly feasible - Recommended**

The did:jwk method is very suitable for our use case:

- **No external dependencies**: Completely self-contained like did:key
- **Deterministic resolution**: DID Document derived from encoded JWK
- **JWK standard**: Uses RFC 7517, widely supported
- **Simple implementation**: Base64url decode → parse JWK → generate document

**Advantages over did:key:**
- Uses familiar JWK format (common in OAuth/OIDC)
- Self-documenting key type via `kty`, `crv` properties
- May be easier to integrate with existing enterprise tooling

**Disadvantages vs did:key:**
- Longer identifiers (JSON vs binary)
- No 1:1 uniqueness guarantee (serialization variations)
- Less compact

### 2. Ecosystem

**Growing / Standards-based**

- **JWK ecosystem**: JWK is a mature IETF standard
- **OAuth/OIDC alignment**: Familiar to enterprise developers
- **Universal Resolver**: Gaining support
- **EBSI adoption**: Used in European digital identity initiatives

### 3. Stability

**Stable**

- **Based on RFC 7517**: Mature, stable standard
- **Simple specification**: Minimal complexity
- **Clear limitations**: Well-documented constraints

## Special Considerations

### Limitations

- **No key rotation**: Same as did:key - key change = new DID
- **No updates/deactivation**: Not supported
- **No uniqueness guarantee**: Different serializations = different DIDs
- **No possession proof**: Requires separate challenge-response

### Use Cases

- Short-lived authentication
- Development/testing
- Enterprise integration (JWK familiarity)
- OAuth/OIDC bridges

## Recommendation

**Recommended**

The did:jwk method should be supported alongside did:key:

1. **Self-contained**: Same benefits as did:key - no external dependencies
2. **JWK familiarity**: Easier for developers familiar with OAuth/OIDC
3. **Simple implementation**: Base64url + JSON parsing
4. **Enterprise-friendly**: JWK is common in enterprise identity

**Implementation notes:**
- Support same algorithms as did:key (Ed25519, P-256, secp256k1)
- Handle `use` property for keyAgreement inclusion
- Document serialization variance issue to users

**Trade-off with did:key:**
- did:key is more compact (multicodec binary encoding)
- did:jwk is more readable and familiar (JSON)
- Both serve similar purposes - support both for maximum compatibility
