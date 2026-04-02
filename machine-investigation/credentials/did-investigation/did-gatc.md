# did:gatc (Gataca)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/gatacaid/gataca-did-method) |
| Organization | Gataca |
| DID Format | `did:gatc:<32-char-base58-identifier>` |

## Overview
Gataca is a blockchain-based digital identity platform providing Identity and Access Management (IAM) services. The did:gatc method uses smart contracts on Ethereum (or private networks like Hyperledger Fabric, Besu, or Quorum) for DID management. It supports multiple DIDs per user (one root plus relationship-specific DIDs) to protect privacy.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires blockchain interaction through the Gataca Backbone middleware. The system depends on smart contracts deployed on Ethereum or private consortium networks. Write operations are restricted - only Gataca can modify the smart contract, making it a permissioned system.

### 2. Ecosystem
Small ecosystem centered around Gataca's commercial identity platform. Limited adoption outside their specific IAM use cases. The project appears to be primarily for Gataca's own services rather than general-purpose DID infrastructure.

### 3. Stability
The specification is documented but tied to a commercial product. Uses ED25519 cryptography by default with GDPR compliance considerations. The dependency on Gataca's infrastructure raises concerns about long-term neutrality.

## Recommendation
**No-go**

Requires external blockchain infrastructure (Ethereum or Hyperledger variants) and depends on Gataca's middleware layer. Node operators cannot verify DIDs without querying external blockchain networks.
