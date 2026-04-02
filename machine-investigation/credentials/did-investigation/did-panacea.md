# did:panacea (Panacea DID Method)

| Property | Value |
|----------|-------|
| Specification | [GitHub - medibloc/panacea-core](https://github.com/medibloc/panacea-core/blob/master/docs/did.md) |
| Organization | MediBloc |
| DID Format | `did:panacea:<base58-encoded-sha256-hash-of-secp256k1-pubkey>` |

## Overview

The did:panacea method is a blockchain-based DID implementation built on the Panacea public blockchain, developed by MediBloc for healthcare data management. The method derives DID identifiers from a base58-encoded SHA-256 hash of a Secp256k1 public key.

Key characteristics:
- **Cryptography**: Uses Secp256k1 key pairs with `Secp256k1VerificationKey2018` verification method
- **Encoding**: Base58 for identifiers, Amino encoding for signatures
- **Document Format**: JSON-LD compliant with W3C DID specification
- **Replay Protection**: Uses incrementing sequence numbers for transaction ordering
- **Deactivation Model**: DIDs can be deactivated but not deleted (prevents malicious recreation)

Example DID: `did:panacea:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm`

DID operations (create, update, deactivate) require submitting transactions to the Panacea blockchain. Resolution requires querying the blockchain state.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

The did:panacea method is inherently tied to the Panacea blockchain:
- DID creation requires submitting transactions to the blockchain
- DID resolution requires querying the blockchain for the current document state
- Verification of DID documents requires access to on-chain state to check:
  - Whether the DID exists and is active
  - The current sequence number
  - The valid verification methods

There is no way to verify a did:panacea identifier or document without accessing the Panacea blockchain. The DID identifier itself is derived from a public key hash, but the actual DID document (containing verification methods, authentication keys, etc.) is stored entirely on-chain.

### 2. Ecosystem

**Limited but active in healthcare niche.**

- **GitHub Activity**: 66 stars, 20 forks, 9 contributors, 31 releases
- **Latest Release**: v2.2.0-1 (March 2024)
- **Real-world Adoption**: 350+ partner hospitals using Dr.Palette (EMR solution built on Panacea)
- **Libraries**: panacea-js SDK available
- **W3C Registry**: Listed in the W3C DID Extensions Methods registry
- **Geographic Focus**: Primarily South Korea healthcare market

The ecosystem is narrowly focused on MediBloc's healthcare applications rather than general-purpose identity use cases. External tooling and libraries outside the MediBloc ecosystem are essentially non-existent.

### 3. Stability

**Moderately stable with active maintenance.**

- The specification has been stable since initial publication
- The blockchain is actively maintained (continuous monitoring and optimization as of Q1 2025)
- Built on Cosmos SDK with IBC support, leveraging mature infrastructure
- No deprecation notices or breaking changes announced
- Focus has shifted toward token economics and healthcare applications rather than DID feature expansion

The core DID functionality appears frozen/stable, though this may indicate limited ongoing development of the identity layer itself.

## Special Considerations

1. **Healthcare Domain**: Designed specifically for healthcare data exchange, which may include regulatory considerations (HIPAA, GDPR for health data)

2. **Controller Restriction**: The specification mandates that "controller in verificationMethod must be equal to the DID subject" - meaning no delegated control is permitted

3. **No Service Endpoints**: The current specification lacks service endpoint fields

4. **Privacy Guidance**: Recommends against including PII and suggests using unique DIDs per interaction

5. **Cosmos/IBC Integration**: The blockchain supports Inter-Blockchain Communication, potentially allowing future cross-chain DID operations

## Recommendation

**No-go**

The did:panacea method cannot be supported in our system due to fundamental architectural incompatibility:

1. **Blockchain Dependency**: All DID operations require direct access to the Panacea blockchain. There is no offline verification mechanism.

2. **Resolution Requires HTTP/RPC**: Resolving a DID document requires querying Panacea blockchain nodes, which violates our constraint that nodes cannot make HTTP requests to validate DIDs.

3. **State Dependency**: Unlike self-certifying DID methods where the identifier cryptographically commits to its verification material, did:panacea stores all document data on-chain. The DID string alone provides no verification capability.

4. **No Embedded Document Pattern**: The method does not support embedding verification material in the DID itself or in accompanying proofs that could be verified offline.

For systems that cannot access external blockchain state, did:panacea is fundamentally unsuitable. Consider blockchain-agnostic methods like did:key or did:jwk that enable fully offline verification.
