# did:tr (Turkey SSI)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/bzlab/ssi-turkiye-spec/blob/main/DID%20Method%20Registration/TR%20SSI%20DID%20Spec%20ReadMe.md) |
| Organization | TUBITAK BILGEM Blockchain Laboratory |
| DID Format | `did:tr:<UUID>` |

## Overview
did:tr is a DID method for Turkish self-sovereign identity, built on Hyperledger Indy infrastructure. The identifier is a UUID (RFC4122) unique within the Indy network namespace. Developed by TUBITAK (Scientific and Technological Research Council of Turkey).

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Hyperledger Indy blockchain:

- Built on Hyperledger Indy public permissioned ledger
- Trust Anchor role required to publish DIDs
- Follows indy-did-method and sovrin-method specifications
- PII kept off-chain; only DIDs, public keys, service endpoints on-ledger
- CRUD operations via Indy ledger transactions

### 2. Ecosystem
**Regional (Turkey).** Government-backed initiative by TUBITAK. Based on mature Hyperledger Indy technology. Limited to Turkish SSI ecosystem.

### 3. Stability
**Moderate.** Government institution backing. Well-established underlying technology (Indy). Regional scope limits broader adoption.

## Recommendation
**No-go**

Requires Hyperledger Indy blockchain infrastructure. The ledger dependency makes it unsuitable for self-contained operation, regardless of its regional government backing.
