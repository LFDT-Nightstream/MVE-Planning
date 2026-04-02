# did:vvo (Vivvo)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://vivvo.github.io/vivvo-did-scheme/spec/did-method-spec-template.html) |
| Organization | Vivvo Application Studios |
| DID Format | `did:vvo:2wJPyULfLLnYTEFYzByfUR` |

## Overview
did:vvo is a DID method for the Vivvo private ledger, designed exclusively for privacy-preserving self-sovereign identity. The identifier is 21-22 base58-encoded characters derived from a 16-byte UUID (excluding confusing characters 0, O, I, l). Vivvo is not a general-purpose blockchain but a specialized private ledger for DIDs and verifiable claims.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires the Vivvo private ledger:
- Resolution mechanism not detailed in specification (addressed in implementation docs)
- Operates on proprietary Vivvo network infrastructure
- Uses Ed25519 cryptographic keys
- Base58 encoding with Bitcoin/IPFS alphabets
- Vivvo network "in all its incarnations" suggests multiple deployment modes

### 2. Ecosystem
Private, proprietary ecosystem. Vivvo Application Studios governs the entire network. The ledger is purpose-built for identity only, not general-purpose transactions. Limited to Vivvo's own applications and infrastructure.

### 3. Stability
The specification focuses on DID creation and key derivation. Resolution details are deferred to implementation documentation. W3C DID specification compliance claimed.

## Recommendation
**No-go**

Requires the proprietary Vivvo private ledger. The network is controlled entirely by Vivvo Application Studios with no public infrastructure.
