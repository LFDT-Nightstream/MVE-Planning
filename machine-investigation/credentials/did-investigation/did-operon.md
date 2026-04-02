# did:operon (Operon Cloud DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:operon Method Specification](https://operon.cloud/did-method/operon) |
| Organization | Operon Cloud |
| DID Format | `did:operon:<method-specific-id>` |

## Overview
The did:operon method is a decentralized identifier system operated by Operon Cloud. It provides a simplified approach to DIDs with a flat, single-segment identifier structure. The method-specific identifier consists of letters, digits, dashes (-), dots (.), or underscores (_).

DID documents are stored in Operon Cloud's centralized Registry Service. Resolution is performed via HTTP GET requests to their public endpoint at `https://did.operon.cloud/1.0/identifiers/{did}`. The method supports standard DID operations including create (via authenticated API calls), update (key rotation, verification relationship modification, service endpoint management), and deactivate (irreversible, marked via metadata flag).

The specification supports JsonWebKey2020 verification methods with Ed25519, P-256, and secp256k1 curves. Domain authority is proven through a well-known DID configuration file at `https://operon.cloud/.well-known/did-configuration.json`. Future versions may optionally support anchoring to Hedera ledger.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for offline verification.** The did:operon method requires HTTP calls to Operon Cloud's centralized resolver at `https://did.operon.cloud/1.0/identifiers/{did}` for DID resolution. There is no way to verify DIDs without making external network requests to their proprietary service. The DID documents are not self-certifying - the identifier does not encode the public key or any cryptographic material that would allow independent verification.

### 2. Ecosystem
**Limited ecosystem.** Operon Cloud appears to be a niche provider with no widely-known adoption metrics. There is no evidence of broad community support, third-party libraries, or significant developer tooling beyond what Operon Cloud provides directly. The method is not listed among commonly implemented DID methods in major identity frameworks.

### 3. Stability
**Early stage/Draft.** The specification is explicitly marked as "Status: Draft" at version 0.2.0, indicating it has not reached production maturity. The specification notes current limitations including no support for hierarchical multi-segment identifiers. The mention of potential future Hedera ledger integration suggests the architecture may still evolve significantly.

## Special Considerations
- **Centralized dependency**: All DID operations require interaction with Operon Cloud's proprietary services
- **Simple identifier format**: Only single flat segments allowed (no hierarchical namespacing)
- **Privacy consideration**: DIDs must avoid personally identifiable information per the spec
- **Deactivation is irreversible**: Once deactivated, a DID cannot be reactivated
- **Potential Hedera integration**: Future versions may anchor to Hedera ledger, which could change the verification model

## Recommendation
**No-go**

The did:operon method is fundamentally incompatible with our key constraint that nodes cannot make HTTP requests to validate DIDs. Resolution requires calls to Operon Cloud's centralized API endpoint, and there is no cryptographic binding between the DID identifier and the DID document that would allow offline verification. The DID format (`did:operon:customerA_channel7`) contains no embedded public key or self-certifying data - verification is entirely dependent on trusting and querying Operon Cloud's registry service. Additionally, the specification is still in draft status (v0.2.0), the ecosystem adoption appears minimal, and the centralized architecture creates a single point of failure that contradicts the decentralization goals of DIDs.
