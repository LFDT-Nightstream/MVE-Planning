# did:webs (Web + KERI)

| Property | Value |
|----------|-------|
| Specification | [Trust over IP](https://trustoverip.github.io/tswg-did-method-webs-specification/) |
| Organization | Trust over IP Foundation / KERI Community |
| DID Format | `did:webs:<domain>:<AID>` |

## Overview
did:webs combines did:web's ease of discovery with KERI (Key Event Receipt Infrastructure) for cryptographic security. The method uses Autonomic Identifiers (AIDs) - self-certifying identifiers bound to Key Event Logs (KELs). An analogy: did:webs is to did:web what HTTPS is to HTTP. The specification is developed by the Trust over IP Foundation's KERI Suite Working Group.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry, but has interesting properties.** Requires:
- Web server for DID document hosting (like did:web)
- DNS infrastructure for domain resolution
- KERI infrastructure for key event logs and witnesses
- Understanding of KERI concepts (KEL, AIDs, witnesses)

However, the KERI components provide self-certifying properties that are partially independent of web infrastructure. The KEL provides cryptographic proof of key history.

### 2. Ecosystem
Strong ecosystem backing:
- Trust over IP Foundation governance
- KERI community (WebOfTrust project)
- Reference implementation at Hyperledger Labs (did-webs-resolver)
- Regular meetings under KERI Suite Working Group
- Presented at Internet Identity Workshop (IIW)

### 3. Stability
Under active development with public review completed (December 2023). Builds on well-established KERI specifications. The Trust over IP Foundation provides institutional backing. More mature than many other DID methods.

## Recommendation
**Requires investigation**

While did:webs requires web infrastructure for discovery, the KERI integration provides self-certifying properties that could be valuable. The KERI components (KEL, AIDs) are cryptographically self-contained. Worth investigating whether KERI's autonomic identifiers could be used independently of the web discovery layer.
