# did:ssw (SK telecom SSW)

| Property | Value |
|----------|-------|
| Specification | [SK telecom](https://sktston.github.io/ssw-did/did-method-spec.html) |
| Organization | SK telecom |
| DID Format | `did:ssw:[network]:<21-22 base58 chars>` |

## Overview
did:ssw is a DID method for the Initial network, a consortium blockchain operated by trusted institutions in South Korea based on SK telecom's ston ledger technology. Uses base58-encoded UUIDs (Bitcoin/IPFS alphabets) and Ed25519 verification keys.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Initial network blockchain:

- Consortium blockchain specific to South Korean institutions
- DID documents stored and managed in Initial network
- Create requires administrator authorization
- Read is public; Update/Delete by owner or administrator
- All write operations require digital signatures

### 2. Ecosystem
**Regional (South Korea).** Limited to SK telecom's consortium network and partner institutions. Not globally accessible.

### 3. Stability
**Moderate.** Enterprise-backed with clear governance model. Limited public documentation.

## Recommendation
**No-go**

Requires the Initial consortium blockchain, which is regionally restricted and operated by specific South Korean institutions. Not suitable for self-contained or globally accessible use.
