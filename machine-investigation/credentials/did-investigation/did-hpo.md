# did:hpo (Hippocrat Protocol)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/hippocrat-protocol/hpo-did-method-spec/blob/main/HPO-DID-method-spec.md) |
| Organization | Hippocrat Protocol |
| DID Format | `did:hpo:<bitcoin-pubkey-hex>` |

## Overview
The did:hpo method is developed by Hippocrat Protocol as a self-sovereign data protocol. DIDs are based on the first public key hex, using the same format as Bitcoin public keys. The method emphasizes local storage for self-sovereignty, with optional peer-to-peer storage for status information. Keys are derived using Bitcoin specifications (BIP32, BIP44).

## Evaluation

### 1. Feasibility/Complexity
**Potentially interesting for investigation.** The specification indicates a decentralized approach where users maintain DIDs in local storage. There's no mention of required HTTP resolution endpoints or blockchain dependencies for basic resolution. However, issuers may use peer-to-peer storage for status information, which would require network access.

The immutable design (update not supported) simplifies verification - if you have the DID and its associated key material, verification could potentially be self-contained. However, the full resolution flow needs clarification.

### 2. Ecosystem
Small ecosystem focused on self-sovereign data, likely healthcare-related given the "Hippocrat" naming. The hippocrat-wallet-sdk provides local DID management. Limited community adoption visible.

### 3. Stability
Early-stage specification with privacy-focused design. The write-once immutability is a notable design choice that simplifies the verification model. Uses well-established Bitcoin cryptographic standards.

## Recommendation
**Requires investigation**

The local storage approach and Bitcoin-style key derivation suggest potential for self-contained verification. However, clarification is needed on whether peer-to-peer storage is required for resolution or only for optional status information. The immutable design is favorable for ledger-based verification.
