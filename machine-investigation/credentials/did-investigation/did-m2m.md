# did:m2m (SmartM2M / Hyperledger Indy)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/m2mblockchain/DID-Method-m2m/blob/main/README.md) |
| Organization | SmartM2M Co., Ltd (South Korea) |
| DID Format | `did:m2m:<base58_22_chars>` |

## Overview
SmartM2M DID is a blockchain-as-a-service solution built on Hyperledger Indy. The identifier is derived from ECC public keys encoded in Base58 (22 characters). The method integrates with the ACCIO BaaS platform and supports DIDComm v2 for agent messaging and Hyperledger AnonCreds for credential issuance.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- Hyperledger Indy blockchain for DID and credential storage
- ACCIO BaaS platform for agent infrastructure
- Mediator services for agent communication
- Dedicated agent deployments for issuers, holders, and verifiers

The architecture is heavily dependent on the Hyperledger Indy ecosystem and SmartM2M's BaaS platform.

### 2. Ecosystem
Built on Hyperledger Indy, which has a mature ecosystem for enterprise identity. SmartM2M focuses on Korean market with SSI/DID integration services. The support for AnonCreds provides privacy-preserving credential capabilities.

### 3. Stability
The method follows W3C DIDs v1.0 and DIF DID Resolution v0.3 specifications. Hyperledger Indy is a well-established platform with broad enterprise adoption. The ACCIO BaaS platform adds a proprietary layer but provides managed infrastructure.

## Recommendation
**No-go**

Requires Hyperledger Indy blockchain and ACCIO BaaS platform infrastructure. Cannot function as a self-contained method.
