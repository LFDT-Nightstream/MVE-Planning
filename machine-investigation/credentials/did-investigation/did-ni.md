# did:ni (Named Information)

| Property | Value |
|----------|-------|
| Specification | [RFC 6920: Naming Things with Hashes](https://www.rfc-editor.org/rfc/rfc6920.html) |
| Organization | IETF (Internet Engineering Task Force) |
| DID Format | `did:ni:<algorithm>;<base64url-encoded-hash>` |

## Overview

The did:ni method is based on RFC 6920's Named Information (ni:) URI scheme, which provides a standardized way to identify digital objects using cryptographic hash outputs. The identifier is derived by hashing content (such as a public key or DID document) and encoding the result in base64url format.

The format follows: `did:ni:<authority>/<algorithm>;<digest-value>`

Where:
- **authority** (optional): A domain that may assist in locating the referenced object
- **algorithm**: The hash algorithm used (e.g., sha-256)
- **digest-value**: The base64url-encoded hash output (no padding)

Example: `did:ni:sha-256;f4OxZX_x_FO5LcGBSKHWXfwtSx-j1ncoSt3SABJtkGk`

The key principle is that the identifier cryptographically binds to the content it references, enabling verification by re-computing the hash and comparing it to the identifier.

## Evaluation

### 1. Feasibility/Complexity

**Can support without external dependencies: YES**

This method is highly feasible for offline verification:

- **Self-certifying**: The DID itself contains all information needed for verification. Nodes can verify the DID by re-hashing the DID document and comparing the result to the identifier.
- **No HTTP calls required**: Verification is purely computational - hash the content and compare.
- **No blockchain access needed**: The identifier is derived from content, not stored on any ledger.
- **Mandatory algorithm**: SHA-256 is required by the specification, which is widely available in all programming environments.
- **Deterministic**: Given the same input, the hash will always produce the same identifier.

The only complexity is ensuring the canonical form of the content being hashed is well-defined (e.g., for DID documents, a canonical JSON representation would be needed).

### 2. Ecosystem

**Limited adoption**

- RFC 6920 is an established IETF standard (published April 2013)
- The ni: URI scheme is registered with IANA
- Libraries exist in multiple languages (Go, Perl, Ruby, JavaScript)
- However, did:ni as a formal DID method is **not registered** in the W3C DID Method Registry
- No mainstream DID libraries appear to implement did:ni natively
- The concept is similar to other self-certifying methods like did:key

### 3. Stability

**Underlying spec is stable; DID method is informal**

- RFC 6920 is a Proposed Standard from 2013 - mature and stable
- The hash algorithm registry at IANA is well-maintained
- SHA-256 support is mandatory, ensuring long-term compatibility
- However, the did:ni method itself appears to be an informal proposal rather than a formally specified DID method
- No version history or formal maintenance process for the DID method layer

## Special Considerations

1. **Content-addressable**: Unlike did:key which embeds a public key, did:ni can identify any hashable content, making it more flexible but also requiring clear conventions about what is being hashed.

2. **Truncated hashes**: RFC 6920 supports truncated hash variants (sha-256-128, sha-256-64, etc.) which trade security for shorter identifiers. Implementations should carefully consider the security implications.

3. **No key rotation**: Like did:key, a did:ni identifier is permanently bound to specific content. Key rotation requires creating a new DID.

4. **Authority field**: The optional authority component could be used for resolution hints, but should not be required for verification.

5. **Query parameters**: RFC 6920 supports query parameters (e.g., `?ct=text/plain`) for metadata, which could extend DID functionality.

6. **Similarity to did:key**: Conceptually very similar to did:key, but did:ni is more general (can hash any content, not just keys) and uses a different encoding scheme.

## Recommendation

**Recommended**

The did:ni method is well-suited for systems where nodes cannot make external HTTP requests or access blockchain state:

1. **Fully self-verifiable**: Verification requires only re-computing a hash, which can be done entirely offline with no external dependencies.

2. **Standards-based**: Built on the mature RFC 6920 IETF standard with IANA-registered algorithm identifiers.

3. **Simple implementation**: The core logic (hash + base64url encode) is straightforward to implement in any language.

4. **Deterministic resolution**: Given the same DID document, any node will derive the same DID, ensuring consistency across the network.

The main considerations are:
- Need to define canonical serialization for DID documents to ensure consistent hashing
- The DID method layer is informal and may need local specification
- No key rotation support (acceptable for many use cases)

For a ledger system that requires offline DID verification, did:ni provides an elegant, standards-based solution similar to did:key but with additional flexibility.
