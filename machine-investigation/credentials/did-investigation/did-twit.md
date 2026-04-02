# did:twit (Twitter DID)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/did-twit/did-twit/blob/master/spec/index.md) |
| Organization | DID Twit Community |
| DID Format | `did:twit:<twitter-username>` |

## Overview
did:twit ties DID identifiers to Twitter accounts, using tweets as the mechanism for publishing and rotating keys. The creation tweet contains the public key, and key rotations require publishing new tweets with proofs. Similar to did:key in wrapping a single public key.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method fundamentally requires Twitter platform:

- Resolution requires reading the Creation Tweet from user's Twitter feed
- Users should pin or highlight creation tweet for discoverability
- Key rotation via new tweet with proof of previous key ownership
- Twitter character limits create poor UX (creation tweet ~850 chars)
- Relies on trusting Twitter for authentication

Linked Data Signatures via proof field required for cryptographic verification.

### 2. Ecosystem
**Very small.** Experimental/research project. The specification URL returns 404, indicating potential abandonment.

### 3. Stability
**Experimental.** Poor user experience due to Twitter limitations. Specification may be unmaintained.

## Recommendation
**No-go**

Requires Twitter platform access for resolution and key management. The dependency on a centralized social media platform makes it fundamentally unsuitable for self-contained or decentralized use.
