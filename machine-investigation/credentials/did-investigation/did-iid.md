# did:iid (Inspur Chain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/InspurIndustrialInternet/iid/blob/main/doc/en/InspurChain_DID_protocol_Specification.md) |
| Organization | Inspur Industrial Internet |
| DID Format | `did:iid:<base58-encoded-address>` |

## Overview
The did:iid method is built on the Inspur Chain blockchain network, targeting people, enterprises, devices, and digital objects seeking decentralized identification. DIDs use base58-encoded addresses derived from cryptographic keypairs. Supports both elliptic curve cryptography (Secp256k1) and RSA. Features master/slave keypair recovery mechanism where slave keys enable recovery if master keys are lost.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution relies on Inspur Chain blockchain infrastructure. DID Description Objects (DDOs) are stored on-chain as immutable records accessible to network participants. Requires connection to Inspur Chain nodes for document retrieval.

### 2. Ecosystem
Enterprise ecosystem within Inspur's industrial internet initiative. Inspur is a major Chinese technology company, providing commercial backing. Focus on industrial IoT and enterprise identity use cases. Limited visibility outside Chinese enterprise market.

### 3. Stability
Version 1.0.0 specification with enterprise backing. Uses established cryptographic standards (Secp256k1, RSA). Privacy protection through local private key storage. However, tied to specific enterprise blockchain infrastructure.

## Recommendation
**No-go**

Requires Inspur Chain blockchain access for DID resolution. Node operators cannot verify DIDs without connecting to the Inspur Chain network.
