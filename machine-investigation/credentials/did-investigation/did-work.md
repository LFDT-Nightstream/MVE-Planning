# did:work (Workday)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://workday.github.io/work-did-method-spec/) (404) |
| Organization | Workday, Inc. |
| DID Format | `did:work:abcdefghi` |

## Overview
did:work is a DID method developed by Workday, Inc. for their credentialing platform. The method is designed for verifiable credentials in enterprise HR contexts. Schema identifiers use method-specific parameters (e.g., `did:work:abcdefghi;schema=17de181feb67447da4e78259d92d0240;version=1.0`). Notably, Workday's implementation diverges from W3C standards - it uses JSON with JSON Schema Draft 7 validation instead of JSON-LD.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires Workday's distributed ledger:
- DIDs and schemas stored on Workday's proprietary ledger
- Resolution queries Workday's infrastructure
- Designed for enterprise HR credentialing use cases
- Non-standard JSON format (not JSON-LD compliant)
- The main specification URL (workday.github.io) returns 404

### 2. Ecosystem
Enterprise-focused ecosystem. Workday is a major HR/finance software company. The method is designed for their specific credentialing use case. Schema versioning follows MODEL.REVISION format for compatibility management.

### 3. Stability
**Concerning stability issues:**
- The main specification URL returns 404
- The specification explicitly states it should NOT be assumed W3C compliant
- Workday "forked" from W3C model due to standard being under development
- This creates interoperability concerns with standard DID tooling

## Recommendation
**No-go**

Requires Workday's proprietary distributed ledger infrastructure. The specification is no longer accessible (404), and the method intentionally diverges from W3C standards, limiting interoperability.
