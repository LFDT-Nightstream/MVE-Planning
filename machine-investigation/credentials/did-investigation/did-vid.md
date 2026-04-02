# did:vid (VP)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/vpayment/did-method-spec/blob/master/vid.md) |
| Organization | VP Inc. |
| DID Format | `did:vp:<40-hex-characters>` |

## Overview
did:vid (also referred to as did:vp) is a DID method using a 40-character hexadecimal identifier on the "VP blockchain." The system uses Redundant Byzantine Fault Tolerance (RBFT) consensus. CRUD operations are performed via HTTP POST requests to designated endpoints. The specification references W3C standards for DIDs and Verifiable Claims.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires the VP blockchain infrastructure:
- CRUD operations via HTTP POST to VP blockchain endpoints (/create, /read, /update, /delete)
- Uses RBFT consensus on VP blockchain
- Resolution queries the VP blockchain system
- Signature-based authorization for updates/deletions
- The specification has incomplete sections, suggesting ongoing development

### 2. Ecosystem
Limited information about VP Inc. and the VP blockchain. The specification references "www.vp.com" as example infrastructure but provides little detail about the actual network. Appears to be a proprietary blockchain for payment/identity use cases.

### 3. Stability
The specification is incomplete in places (e.g., resolution section notes "MUST specify how a client uses a DID"). This suggests the method is still under development. Supports Ed25519 and RSA verification key types.

## Recommendation
**No-go**

Requires the VP blockchain infrastructure. The specification is incomplete, and limited public information exists about the VP network and organization.
