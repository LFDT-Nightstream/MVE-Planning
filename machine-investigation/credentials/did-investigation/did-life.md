# did:life (LifeID)

| Property | Value |
|----------|-------|
| Specification | [LifeID Spec](https://lifeid.github.io/did-method-spec/) |
| Organization | LifeID Foundation |
| DID Format | `did:life:<75_byte_encoded_id>` |

## Overview
LifeID DID is a self-sovereign identity method where the encoded DID contains all necessary data to locate its corresponding document. The 75-byte identifier encodes version, network ID, type (individual/organization/device), public key, and SHA3-256 checksum. The method claims blockchain-agnostic smart contract support.

## Evaluation

### 1. Feasibility/Complexity
**Mixed self-contained and infrastructure dependencies.** The specification states the encoded DID "already contains all the data necessary to obtain the location of its corresponding DID Document." However:

- Current implementation recommends Ethereum for DID storage
- RChain identified as future candidate
- API bridge provides RESTful access to distributed ledger functions
- Smart contracts manage keys, credentials, and revocation

The self-describing identifier is a positive feature, but practical resolution still requires blockchain infrastructure.

### 2. Ecosystem
The LifeID Foundation maintains the specification with ZNO Labs contributing. The support for multiple entity types (individuals, organizations, devices) indicates broad applicability goals.

### 3. Stability
The specification is well-documented with clear identifier structure. The blockchain-agnostic design goal is positive, but current implementation relies on Ethereum. The checksum-protected identifier format provides data integrity assurance.

## Recommendation
**Requires Investigation**

The self-describing identifier concept is interesting - the DID itself contains location data for its document. However, current implementations require blockchain infrastructure. Investigate whether the self-describing nature could be leveraged for local-first resolution in specific use cases.
