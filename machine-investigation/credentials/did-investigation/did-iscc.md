# did:iscc (ISCC - International Standard Content Code)

| Property | Value |
|----------|-------|
| Specification | [IEP-0015](https://ieps.iscc.codes/iep-0015/) |
| Organization | ISCC Foundation |
| DID Format | `did:iscc:<base32_encoded_iscc_id>` |

## Overview
ISCC DID is a decentralized identifier method for content identification using the International Standard Content Code framework. It operates through a federation of public ledgers supporting the ISCC declaration protocol. The method enables content identification through similarity-preserving hashes that can match transcoded or transformed digital media.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- A federation of public blockchain networks supporting the ISCC declaration protocol
- ISCC Content Registry instances for DID resolution
- On-chain transactions for authenticity verification
- Controllers assigned via `did:pkh` format referencing blockchain accounts

Resolution queries an ISCC registry, with authenticity verified through referenced on-chain transactions.

### 2. Ecosystem
Specialized ecosystem focused on content identification and digital media. The ISCC standard itself has adoption in content authentication use cases, but the DID method is a specialized application.

### 3. Stability
The specification is documented through ISCC Enhancement Proposals (IEPs) and registered with W3C. The reliance on multiple blockchain networks provides some redundancy but also complexity.

## Recommendation
**No-go**

Requires federation of public blockchains and ISCC Content Registry infrastructure. Specifically designed for content identification rather than general-purpose identity.
