# did:self (Self-Certified DID)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/excid-io/did-self) |
| Organization | Nikos Fotiou (EXCID) |
| DID Format | `did:self:<base64url-jwk-thumbprint>` |

## Overview
did:self is a registry-free DID method where identifiers are JWK thumbprints (RFC 7638). DID documents are self-certified via JSON Web Signatures (JWS), with the document itself as the payload. No external registry or blockchain required.

## Evaluation

### 1. Feasibility/Complexity
**Self-contained.** This method requires no external infrastructure:

- Identifier is JWK thumbprint of the public key
- Documents self-certified via JWS signatures
- Can be included in messages, stored in files, or served via DNS
- Validation: verify thumbprint matches DID, then verify JWS signature
- Deliberately allows multiple valid documents per identifier (for group scenarios)

The only verification needed is cryptographic signature validation.

### 2. Ecosystem
**Small.** Python reference implementation exists (`did-self-py`). Academic/research origins. Limited but growing adoption.

### 3. Stability
**Moderate.** Clean, well-documented specification. Follows established standards (JWK, JWS, RFC 7638).

## Recommendation
**Recommended**

did:self is one of the few truly self-contained DID methods. It requires no external infrastructure - documents are self-certified via cryptographic signatures. The identifier is cryptographically bound to the public key, enabling verification without any external lookups. Excellent candidate for a ledger-based registry that needs to operate independently.
