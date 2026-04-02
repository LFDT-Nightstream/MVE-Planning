# did:hpass (IBM Digital Health Pass)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/IBM/hpass/blob/main/doc/did-spec.md) |
| Organization | IBM (Watson Works) |
| DID Format | `did:hpass:<genesis-hash>:<identifier>` |

## Overview
The did:hpass method is part of IBM Digital Health Pass, a solution for health status attestation built on Hyperledger Fabric blockchain. DIDs are network-specific (genesis hash prevents migration between networks), with identifiers derived from hashing MSP identifier and Fabric identity. Designed for Health Authorities and Credential Issuers managing cryptographic material for digital health credentials.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution relies on Hyperledger Fabric blockchain infrastructure. DID documents are fetched directly from the Fabric network, with authenticity derived from blockchain properties. Requires Fabric peers, orderers, and chaincode implementing CRUD operations.

### 2. Ecosystem
Enterprise ecosystem within IBM's digital credentials portfolio. Designed for healthcare credential issuance during the COVID-19 pandemic era. Health Authorities (ministries, governments, companies, testing centers) control the network. Limited to Health Credential Issuer entities rather than general public use.

### 3. Stability
Production-grade specification backed by IBM. Built on mature Hyperledger Fabric foundation. Well-documented with clear privacy considerations (no PII in DID documents). However, highly specific to healthcare credentialing use case.

## Recommendation
**No-go**

Requires Hyperledger Fabric blockchain infrastructure for DID resolution. Node operators cannot verify DIDs without access to a Fabric network with deployed chaincode.
