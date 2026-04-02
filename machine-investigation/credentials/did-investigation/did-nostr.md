# did:nostr (Nostr DID Method)

| Property | Value |
|----------|-------|
| Specification | [Nostr DID Method](https://nostrcg.github.io/did-nostr/) |
| Organization | W3C Nostr Community Group (Editor: Melvin Carvalho) |
| DID Format | `did:nostr:<64-char-hex-pubkey>` |

## Overview
The did:nostr method provides Decentralized Identifiers for the Nostr protocol ecosystem. The DID is derived directly from a secp256k1 public key encoded as a 64-character lowercase hexadecimal string. No on-chain registration is required - the security and uniqueness are derived from the key generation process itself.

Example: `did:nostr:124c0fa99407182ece5a24fad9b7f6674902fc422843d3128d38a0afbee0fdd2`

The specification defines a three-tier resolution strategy:
1. **Offline-First (Minimal) Resolution**: Resolvers can generate valid DID documents using only the public key, without any network access
2. **HTTP Resolution**: Servers can optionally host documents at well-known URLs
3. **Enhanced Resolution**: Optional Nostr relay queries can augment documents with metadata and social graph information

DID documents include standard verification methods using the Multikey format, with support for authentication and assertion methods. Optional fields include service endpoints (relay lists), profile metadata, and social graph (follows) information.

## Evaluation

### 1. Feasibility/Complexity
**Excellent** - This method is highly suitable for offline verification.

The specification explicitly supports "Offline-First (Minimal) Resolution" where resolvers can generate valid DID documents using only the public key without any network access. The DID itself contains all the cryptographic material needed:
- The public key is embedded directly in the DID (64-character hex string)
- Secp256k1 is a well-supported elliptic curve with mature libraries
- Schnorr signatures (used by Nostr) can be verified locally
- The Multikey format transformation is deterministic and can be computed locally

No blockchain access or HTTP calls are required for basic DID resolution and signature verification. Enhanced features (profile metadata, relay lists, social graph) would require network access but are explicitly optional.

### 2. Ecosystem
**Moderate to Growing** - Nostr has a significant and active community.

- Nostr protocol has gained substantial adoption, particularly for decentralized social networking
- Multiple client implementations exist across platforms
- Active developer community with regular protocol improvements (NIPs)
- However, did:nostr specifically is relatively new and adoption is still emerging
- Secp256k1 libraries are widely available (used by Bitcoin, Ethereum, etc.)
- The W3C Nostr Community Group provides some institutional backing

### 3. Stability
**Early Stage / Draft**

- Current status: Unofficial draft specification (version 0.0.10)
- Explicitly marked as "work in progress" that may be updated or replaced
- Not a W3C Standard nor on the W3C Standards Track
- The specification is actively maintained but subject to change
- Core cryptographic foundations (secp256k1, Schnorr) are stable and well-established

## Special Considerations

**Strengths:**
- No registration required - DIDs are self-certifying based on key generation
- Explicit offline-first resolution support built into the spec
- Uses widely-supported secp256k1 cryptography
- Clean integration with existing Nostr ecosystem
- Simple, deterministic DID-to-document transformation

**Limitations:**
- No support for DID document updates after creation
- No support for DID deactivation or key revocation
- Identifiers are permanently linked to their generating key pair
- Loss of private key means permanent loss of identifier control
- DIDs must use raw hex format, not the more user-friendly npub Bech32 encoding
- All associated metadata is publicly resolvable on relays (privacy consideration)

**Technical Notes:**
- Uses Multikey verification format with multibase base16-lower encoding
- Requires parity byte (0x02 or 0x03) prepending for W3C VC compatibility
- Multicodec varint for secp256k1 is 0xe7, 0x01

## Recommendation
**Recommended**

The did:nostr method is well-suited for our use case due to its explicit offline-first resolution capability. Nodes can verify DIDs and signatures without making any HTTP requests or accessing external blockchain state. The DID format embeds the full public key, making verification purely computational.

Key advantages:
1. Self-certifying identifiers require no external lookups
2. Secp256k1 is a mature, well-supported curve with existing library implementations
3. The specification explicitly defines offline resolution as a first-class feature
4. Simple and deterministic transformation from DID to verification key

The main trade-offs are the lack of key rotation/revocation support and the early-stage spec status. However, for use cases where immutable key-bound identifiers are acceptable, this method provides an excellent balance of simplicity and decentralization. The growing Nostr ecosystem also provides a pathway for broader adoption and tooling support.
