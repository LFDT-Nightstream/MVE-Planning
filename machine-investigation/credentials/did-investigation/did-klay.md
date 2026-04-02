# did:klay (Klaytn)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/ontology-tech/DID-method-specs/blob/master/did-klay/DID-Method-klay.md) |
| Organization | Ontology Tech |
| DID Format | `did:klay:<40_hex_chars>` |

## Overview
Klaytn DID is a blockchain-based decentralized identifier method for the Klaytn network. DIDs are implicitly created when a subject obtains a Klaytn address - the DID is simply `did:klay:` concatenated with the address (minus the "0x" prefix). Resolution and management occur through smart contract interactions.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Klaytn blockchain infrastructure
- DID management smart contract deployment
- `get_document()` method for resolution
- Smart contract methods for controller management and deactivation

The specification notes that clients should "query a certain number of nodes and compare return values" for trustworthiness, indicating full blockchain dependency.

### 2. Ecosystem
Klaytn is a public blockchain with enterprise focus, backed by Kakao (major South Korean tech company). The DID specification was contributed by Ontology Tech, suggesting cross-project collaboration.

### 3. Stability
The specification follows W3C DID Core standards with full CRUD support plus delegation features. The implicit creation model (address = DID) simplifies onboarding but ties identity to blockchain account ownership.

## Recommendation
**No-go**

Requires Klaytn blockchain infrastructure for all operations. Cannot function as a self-contained DID method.
