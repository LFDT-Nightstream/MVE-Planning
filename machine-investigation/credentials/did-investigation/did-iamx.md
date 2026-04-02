# did:iamx (IAMX)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/IAMXID/did-method-iamx/blob/main/IAMX_DID_method.md) |
| Organization | IAMX AG (Switzerland) |
| DID Format | `did:iamx:cardano:<uuid-v4>` |

## Overview
The did:iamx method is developed by IAMX AG, a Swiss company, and operates on the Cardano blockchain. DIDs use UUID v4 format identifiers. The system emphasizes self-sovereign identity generation on user devices, with DIDs registered immutably on the Cardano ledger. Supports multiple key pairs, delegation capabilities, and purpose-built DIDs to prevent correlation.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires lookup in the Cardano Ledger Database through IAMX's backend `didVerify` method, which queries a connected Cardano node. While DID creation happens offline on user devices, verification requires blockchain access to retrieve DID documents.

### 2. Ecosystem
Growing ecosystem within the Cardano community. IAMX AG provides commercial identity services. Privacy-focused design with explicit prohibition of personal data in DID documents. Swiss company registration adds regulatory credibility.

### 3. Stability
Well-documented specification with clear privacy and security considerations. Cardano is a major blockchain with strong academic foundations. The method benefits from Cardano's stability while adding IAMX-specific features.

## Recommendation
**No-go**

Requires Cardano blockchain access for DID resolution. Node operators cannot verify DIDs without querying the Cardano ledger through IAMX backend services or direct Cardano node access.
