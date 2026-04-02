# did:klayr (Klayr Sidechain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/aldhosutra/klayr-did/blob/main/packages/klayr-did-module/docs/did-method-spec.md) |
| Organization | Independent (Aldo Suhartono Putra) |
| DID Format | `did:klayr:<chainspace>:<namespace>:<unique-id>` |

## Overview
Klayr DID is a method designed for the Klayr blockchain ecosystem (formerly Lisk). It supports multi-controller DIDs with hierarchical namespaces, using EdDSA/Ed25519 exclusively for all cryptographic operations. The method requires deployment of a dedicated DID module on Klayr sidechains.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Klayr Application sidechain deployment
- Dedicated Klayr DID Module implementation
- Connection to Klayr Mainchain via Interoperability protocol
- Transaction fees for all operations
- On-chain storage for DID document state

Resolution queries the DID module on the sidechain, making blockchain access mandatory.

### 2. Ecosystem
Specialized ecosystem for Klayr (Lisk rebrand). The method is community-developed rather than officially maintained. The sidechain architecture provides flexibility but adds deployment complexity.

### 3. Stability
The specification adheres to W3C DID Core with comprehensive features:
- Multi-controller support
- Multiple verification relationships
- Service endpoints
- DID-URL support per RFC3986

However, the community-maintained nature and sidechain requirement introduce stability concerns.

## Recommendation
**No-go**

Requires Klayr sidechain infrastructure with dedicated DID module deployment. Cannot operate without the underlying blockchain ecosystem.
