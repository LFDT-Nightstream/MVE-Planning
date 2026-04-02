# did:kilt (KILT Protocol)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/KILTprotocol/specifications/blob/main/docs/did/did-spec.md) |
| Organization | KILT Protocol |
| DID Format | `did:kilt:<did-identifier>` or `did:kilt:light:<encoding><id>` |

## Overview
KILT DID supports two types of identifiers: Full DIDs (on-chain) and Light DIDs (off-chain). Full DIDs are stored on the KILT blockchain (Substrate-based) with SS58-encoded addresses. Light DIDs are self-contained and support basic identity operations without blockchain interaction.

## Evaluation

### 1. Feasibility/Complexity
**Partial self-contained support via Light DIDs.** The method offers:

**Light DIDs (off-chain):**
- Self-contained identifiers without blockchain dependency
- Support for single authentication key and optional key agreement key
- Service details encoded in the DID itself
- Limited to basic operations

**Full DIDs (on-chain):**
- Requires KILT blockchain for management and resolution
- Information stored in blockchain storage maps
- Full feature set including multiple keys and web3 names

The Light DID option provides a path for self-contained operation but with feature limitations.

### 2. Ecosystem
Active ecosystem with the KILT blockchain in production. Features include:
- Light-to-full DID migration without invalidating credentials
- Web3 name support for human-readable aliases
- Multiple cryptographic key types (sr25519, ed25519, ecdsa-secp256k1, x25519)
- DIF Universal Resolver integration

### 3. Stability
Well-documented specification with clear separation between light and full DIDs. The Substrate-based blockchain provides technical stability. Features like DID blacklisting (preventing re-creation after deletion) indicate mature operational considerations.

## Recommendation
**Requires Investigation**

Light DIDs offer a partially self-contained option worth investigating. However:
- Light DIDs have limited functionality compared to full DIDs
- Migration to full DIDs requires KILT blockchain
- The ecosystem is tied to KILT-specific infrastructure for advanced features

Investigate whether Light DID capabilities meet your requirements before committing.
