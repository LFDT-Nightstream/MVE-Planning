# did:omn (OmniOne DID Method)

| Property | Value |
|----------|-------|
| Specification | [did_method.md](https://github.com/OmniOneID/did_method/blob/master/did_method.md) |
| Organization | RaonSecure / OmniOneID (South Korea) |
| DID Format | `did:omn:<alphanumeric-identifier>` (e.g., `did:omn:4EFNaYeA9hDp6F55JAB38EFtNcYEbbM9nwKr`) |

## Overview

The did:omn method is a blockchain-based decentralized identifier system developed by RaonSecure, a South Korean ICT security company. OmniOne is described as "a decentralized network system for Self-Sovereign identity and Verifiable Claims."

The system operates on a custom blockchain using a Delegated Proof of Stake consensus mechanism with Byzantine Fault Tolerance (requiring 2/3+ agreement). DID Documents are stored on-chain, and all CRUD operations (Create, Read, Update, Delete) are performed via smart contracts through RESTful APIs.

Key technical characteristics:
- **DID Syntax**: `did:omn:` followed by alphanumeric characters (per RFC5234)
- **Signature Algorithm**: ECDSA Koblitz (EcdsaKoblitzSignature2016)
- **Security**: Nonces stored on-chain to prevent replay attacks
- **Privacy**: No PII stored; only public keys and signature values for claim verification

## Evaluation

### 1. Feasibility/Complexity

**Assessment: Not feasible without external dependencies**

The did:omn method requires:
- Access to the OmniOne blockchain to resolve DID Documents
- Smart contract interaction for all CRUD operations
- RESTful API calls to OmniOne infrastructure endpoints (`/did_read`, `/did_create`, etc.)

There is no self-certifying mechanism. The DID identifier does not encode the public key or any verifiable cryptographic material. Resolution requires querying the OmniOne blockchain or API servers to retrieve the DID Document.

**Critical Issue**: Nodes cannot verify did:omn identifiers without making HTTP calls to OmniOne infrastructure or having access to OmniOne blockchain state.

### 2. Ecosystem

**Assessment: Regional adoption with active development**

Strengths:
- Significant adoption in South Korea's financial sector (7 major banks including Shinhan, Woori, NH Nonghyup)
- Government backing: Used for South Korean civil servant ID cards; involved in digital driver's license initiative
- Active open-source development: 25+ repositories on GitHub with recent activity (January 2026)
- Mobile SDKs available for Android and iOS
- Part of DID Alliance Korea with members including Samsung and LG

Weaknesses:
- Primarily focused on Korean market
- Limited international adoption outside South Korea
- Specification repository has only 6 commits since May 2019 (minimal spec evolution)

### 3. Stability

**Assessment: Active project, but stale specification**

- **Specification**: Last updated May 2019 (6 commits total); appears largely unchanged since initial publication
- **Implementation**: Very active - did-ta-server has 190 stars, did-ca-server has 195 stars; repositories updated through January 2026
- **Open DID Initiative**: Recently launched open-source program to promote global standardization of "K-DID"
- **License**: Apache 2.0 across all repositories

The specification document itself is quite dated, but the broader OmniOne/Open DID ecosystem continues active development with new features and components.

## Special Considerations

1. **Blockchain Dependency**: OmniOne runs its own permissioned blockchain using EOSIO technology with PoA-BFT consensus. This is not a public blockchain like Ethereum.

2. **Centralized Infrastructure**: Despite being "decentralized," the system relies on predetermined Block Producers rather than open participation. The blockchain is permissioned.

3. **Korean Focus**: Deep integration with Korean government and financial systems. International expansion is a stated goal but not yet realized.

4. **Open DID Initiative**: RaonSecure launched an open-source initiative to share K-DID technology globally, potentially improving accessibility, but the core dependency on OmniOne infrastructure remains.

5. **No Self-Certification**: Unlike did:key or did:peer, the DID identifier itself contains no cryptographic material that could enable offline verification.

## Recommendation

**No-go**

The did:omn method fundamentally requires access to the OmniOne blockchain or API infrastructure to resolve DID Documents and verify identifiers. There is no mechanism to:

1. Derive the public key from the DID identifier itself
2. Verify the DID without external network calls
3. Access OmniOne blockchain state from external systems

Given the core constraint that nodes cannot make HTTP requests to validate DIDs and cannot access external blockchain state, did:omn is incompatible with the system requirements. The identifier `did:omn:4EFNaYeA9hDp6F55JAB38EFtNcYEbbM9nwKr` is opaque and requires lookup against OmniOne's infrastructure to resolve to a DID Document containing the actual public keys.

While OmniOne has achieved notable adoption in South Korea's banking and government sectors, its architecture is designed for environments where participants can communicate with the OmniOne network - not for offline or isolated verification scenarios.
