# did:vtid (VTID / Vertu)

| Property | Value |
|----------|-------|
| Specification | [VERTU](http://my-did.vertu.com/spec/) |
| Organization | VERTU (Contact: 806916678@qq.com) |
| DID Format | `did:vtid:<method-specific-id>` |

## Overview
did:vtid is a DID method associated with VERTU's identity infrastructure, described as part of the "JianKong network." The method emphasizes privacy by storing all private data locally with only hash values or encrypted strings published on-chain. The private key is never exposed, though users can display it locally. DID documents are considered public and should not contain personal information.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires VERTU/JianKong blockchain infrastructure:
- DID operations require access to the JianKong network
- Private data stored locally, hashes/encrypted data on-chain
- Integrity verified through hash value checking
- Record data encrypted and signed with private keys
- Related to but distinct from did:vertu

### 2. Ecosystem
Very limited ecosystem. Appears to be part of VERTU's broader identity initiatives. Contact information is a QQ email address, suggesting Chinese market focus. Limited English documentation available.

### 3. Stability
The specification is hosted on VERTU's infrastructure. Security model requires private key secrecy - compromised keys require immediate DID revocation. Limited public adoption or third-party implementations.

## Recommendation
**No-go**

Requires the JianKong network infrastructure operated by VERTU. Proprietary system with limited documentation and ecosystem support.
