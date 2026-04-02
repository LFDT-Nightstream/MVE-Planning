# did:nuggets (Nuggets DID Method)

| Property | Value |
|----------|-------|
| Specification | [Link](https://github.com/NuggetsLtd/nuggets-did-resolver/blob/master/doc/did-methods/did:nuggets.md) |
| Organization | Nuggets Ltd |
| DID Format | `did:nuggets:<base58-encoded-ethereum-address>` |

## Overview
The Nuggets DID Method is a decentralized identifier system built on top of Nuggets' private Ethereum network. Nuggets is a self-sovereign verified digital identity and payment platform where personal and payment data is owned and controlled by the user.

The method-specific identifier is a Base58 (Bitcoin variant) encoding of an Ethereum address from the Nuggets private ledger. For example, the Ethereum address `0x47dCBa7a9a102338D3dA1198662e138D11185149` converts to the DID `did:nuggets:214udHLePZCeS3QvPczZwk88gwEQ`.

Users create accounts by generating a BIP-39 mnemonic through the Nuggets mobile app (iOS/Android), deriving a hierarchical deterministic wallet and DPKI keypair, passing KYC verification, and registering on the network using a zero-knowledge proof. DID Documents support multiple verification methods including EcdsaSecp256k1VerificationKey2019 and Bls12381G2Key2020.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.**

DID resolution requires HTTP requests to Nuggets' proprietary API endpoint (`https://api-dev.internal-nuggets.life/api/v1/nugget/{ethereum_address}/did`). There is no way to verify or resolve a did:nuggets identifier without making external HTTP calls to the Nuggets infrastructure. The DID Document is not self-describing and cannot be derived from the identifier alone - the identifier is merely a Base58-encoded Ethereum address that maps to an account in their private network.

Additionally, the underlying infrastructure is a private Ethereum network controlled by Nuggets, making it impossible for external nodes to access or verify blockchain state independently.

### 2. Ecosystem
**Limited ecosystem.**

- Maintained by a single company (Nuggets Ltd)
- Primary usage appears to be within the Nuggets identity and payment platform
- No evidence of widespread adoption outside the Nuggets ecosystem
- The resolver is available on GitHub but is specific to Nuggets infrastructure
- No known third-party implementations or integrations

### 3. Stability
**Draft specification, uncertain stability.**

- The specification explicitly states it is "a draft and the information contained herein is subject to change"
- Limited public documentation and community involvement
- Dependent on continued operation of Nuggets Ltd's infrastructure
- Last known activity on the specification repository is not recent

## Special Considerations
- **KYC Requirement**: Account creation requires passing KYC verification, which means DIDs are not pseudonymous and have real-world identity linkage
- **Privacy Model**: Despite being identity-verified, Nuggets claims to have no access to user data - all data is encrypted by users for intended recipients
- **Private Network**: Built on a private Ethereum ledger, not a public blockchain
- **Mobile-Only Creation**: DIDs can only be created through the Nuggets mobile app (iOS/Android)
- **TLS Requirements**: Strong security requirements including TLS 1.2+ with forward secret ciphers and certificate pinning

## Recommendation
**No-go**

The did:nuggets method cannot be supported without external dependencies. Resolution requires HTTP calls to Nuggets' proprietary API infrastructure, and verification depends on state from their private Ethereum network. There is no mechanism for nodes to independently verify DIDs without network access to Nuggets' services.

The method is also tightly coupled to a single vendor's platform, relies on a draft specification, and requires KYC verification for DID creation, which limits its utility as a general-purpose decentralized identifier. The combination of centralized infrastructure dependency and limited ecosystem adoption makes this method unsuitable for integration where external network requests are not permitted.
