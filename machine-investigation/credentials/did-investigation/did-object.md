# did:object (Trusted Digital Web DID Object Method)

| Property | Value |
|----------|-------|
| Specification | [did-object-1-5-0.md](https://github.com/mwherman2000/TrustedDigitalWeb/blob/master/specifications/did-methods/did-object-1-5-0.md) |
| Organization | Trusted Digital Web Project |
| DID Format | `did:object:<KERI-Base64-encoded-public-key>` |

## Overview
The did:object method is part of the Trusted Digital Web (TDW) project and provides decentralized identifiers for "DID Objects." The method-specific identifier is a KERI Base64-encoded public key, where the seed can be any globally unique value such as a database key or GUID.

The DID format follows: `did:object:[id-string]` where the id-string consists of KERI Base64-encoded characters. Example: `did:object:BF5pxRJP6THrUtlDdhh07hJEDKrJxkcR9m5u1xs33bhp`

CRUD operations are performed through a "Trusted Digital Web Runtime Library" that implements interfaces for registration, resolution, key management, and deactivation. The method uses the Stratis Platform blockchain (a C#/.NET-based smart contract platform) as its Verifiable Data Registry (VDR).

Supported cryptographic algorithms include:
- ECDSA: secp224r1, secp256r1, secp384r1, secp521r1
- SM2: sm2p256v1
- EdDSA: ed25519

## Evaluation

### 1. Feasibility/Complexity
**Cannot support without external dependencies.**

The did:object method requires access to the Stratis blockchain to resolve DIDs. All CRUD operations (Create, Read, Update, Deactivate) are performed through a Runtime Library that interacts with the Stratis Platform blockchain. There is no way to verify a did:object DID without querying the Stratis blockchain state to retrieve the DID Document.

The method also depends on:
- KERI key management techniques for identifier generation
- Stratis Platform blockchain for VDR functionality
- A C#/.NET Runtime Library for all operations

Nodes would need to make external calls to the Stratis blockchain to resolve and verify any did:object identifier.

### 2. Ecosystem
**Very limited ecosystem.**

- The Stratis Platform is a relatively obscure blockchain with limited adoption
- No widely-used libraries or tools for did:object outside the TDW project itself
- The project appears to be maintained primarily by a single individual (Michael Herman)
- No evidence of significant community adoption or third-party implementations
- The specification references conformance to W3C DID v0.11 (an outdated version; current is v1.0)

### 3. Stability
**Questionable stability.**

- The specification is at version 1.5.0 (Web 7.0 Sharded Registry)
- References an outdated W3C DID specification (v0.11 instead of current v1.0)
- The project repository shows limited activity and appears to be a single-maintainer project
- No evidence of formal standardization or governance beyond the TDW project itself
- The Stratis blockchain itself has uncertain long-term viability

## Special Considerations
- The method introduces three internal sub-methods (`did:keys`, `did:svc`, `did:proof`) for a "Sharded Registry" architecture
- Once a DID is deactivated (all keys removed), it cannot be reactivated
- No query operators are supported
- The specification is heavily tied to the Microsoft/.NET ecosystem (C#, .NET Core, Visual Studio)

## Recommendation
**No-go**

The did:object method is fundamentally incompatible with our key constraint: nodes cannot make HTTP requests or access external blockchain state to validate DIDs. Since did:object requires querying the Stratis blockchain for all resolution and verification operations, it cannot be supported in our system.

Additionally, the method has:
- Very limited ecosystem adoption
- Dependency on an obscure blockchain (Stratis)
- Questionable long-term maintenance and stability
- Outdated W3C DID specification conformance
- Single-maintainer project with limited community support

Even if external blockchain access were permitted, the limited ecosystem and uncertain future of the Stratis Platform make this method a poor candidate for investment.
