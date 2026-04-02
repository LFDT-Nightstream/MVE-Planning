# did:morpheus (IOP Stack / Hydra)

| Property | Value |
|----------|-------|
| Specification | [IOP Developer Docs](https://developer.iop.global/w3c) (410 Gone) |
| Organization | Internet of People (IOP) |
| DID Format | `did:morpheus:<identifier>` |

## Overview
Morpheus DID is part of the IOP Stack, providing gatekeeper-free identity management as a second layer on top of the Hydra blockchain (an ARK bridgechain). The system supports self-sovereign identity with DIDs based on W3C specifications and verifiable credentials.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure, but claims ledger-agnostic design.** Based on available information:

- Morpheus relies on Hydra blockchain for DID document repository
- Current implementation uses Hydra, but design is "ledger-agnostic in general"
- DID documents establish identity across different cryptographic keys over time
- No personally identifiable information in documents - personal data stored elsewhere

The architecture separates DID state from personal data storage, with the blockchain serving as an append-only log for identity state changes.

### 2. Ecosystem
IOP (Internet of People) ecosystem includes:
- Hydra blockchain (ARK bridgechain)
- Coeus naming system for binding data to names
- Multiple SDK implementations (TypeScript, Rust, Dart)
- Active GitHub development

The project emphasizes W3C standards compliance for interoperability.

### 3. Stability
**Concerning signals:**
- Primary specification URL returns 410 (Gone)
- Documentation may be in transition or deprecated
- The project has undergone significant pivots historically

The underlying technology (KERI-like key management, W3C standards) is sound, but project stability is questionable.

## Recommendation
**No-go**

While the ledger-agnostic design goal is interesting, the current implementation requires Hydra blockchain. More concerning is the 410 (Gone) response from the specification URL, suggesting documentation instability or project transition. Cannot recommend without stable, accessible specifications.
