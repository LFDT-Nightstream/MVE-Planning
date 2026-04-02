# did:v1 (Veres One)

| Property | Value |
|----------|-------|
| Specification | [W3C CCG](https://w3c-ccg.github.io/did-method-v1/) |
| Organization | Digital Bazaar / W3C Credentials Community Group |
| DID Format | `did:v1:nym:<hash>` or `did:v1:uuid:<uuid>` |

## Overview
Veres One is a permissionless public ledger designed specifically for DID management. It supports two identifier types: cryptonym-based (nym) DIDs derived from SHA-256 hashes of public keys, and UUID-based DIDs for entities storing metadata on-ledger. The system uses Ed25519 cryptographic signatures and implements Linked Data Object Capabilities for authorization. Notably, Veres One operates without cryptocurrency - no speculative tokens are required.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** While well-designed, it requires the Veres One ledger:
- Resolution through veresOneReadService HTTP endpoints
- Operations via veresOneOperationsService
- Requires running or connecting to Veres One nodes
- Uses Continuity consensus (leaderless Byzantine fault-tolerant)
- Performance: ~209 tx/sec, 15-30 second consensus latency

Cryptonym-based DIDs can theoretically function as unregistered pseudonymous identifiers, but full functionality requires ledger access.

### 2. Ecosystem
Well-established in the DID community. Digital Bazaar (Manu Sporny, Dave Longley) are key contributors to W3C DID standards. The project has strong technical leadership and governance follows a stakeholder-driven model. However, actual network adoption appears limited.

### 3. Stability
Specification status: CG-DRAFT (Community Group Draft). While the specification is well-written and comprehensive, it remains in draft stage within W3C processes. The design is mature but the network itself has not achieved widespread adoption.

## Recommendation
**No-go**

Despite being well-designed and led by respected DID community members, Veres One requires its own dedicated blockchain infrastructure. The cryptonym variant could theoretically work offline, but practical use requires the Veres One network.
