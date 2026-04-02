# did:ev (Ethereum Verifiable DID Method)

| Property | Value |
|----------|-------|
| Specification | [KayTrust did-method-ev](https://github.com/KayTrust/did-method-ev) |
| Organization | KayTrust |
| DID Format | `did:ev:<MNID>` |

## Overview

The did:ev method is an Ethereum-based DID method that uses smart contracts to manage decentralized identifiers. Each DID corresponds to a "Proxy contract" deployed on an Ethereum-compatible blockchain. The method-specific identifier uses Multi-Network ID (MNID) format, which encodes both an Ethereum address and the network chain ID, allowing DIDs to exist across different EVM-compatible networks.

DID creation involves deploying a Proxy contract (typically via an IdentityManager contract), then encoding the contract address and network ID into an MNID. Resolution requires decoding the MNID, locating the appropriate IdentityManager contract, and scanning CapabilitySet events to build the list of verification methods. The method uses a capability-based system where cryptographic keys are assigned roles (authentication, assertion, key agreement, etc.) through smart contract calls.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

Verification of did:ev DIDs requires:
- Access to Ethereum (or EVM-compatible) blockchain nodes to read smart contract state
- Ability to query IdentityManager contracts and scan CapabilitySet events
- Resolution of MNID to determine which network and contract address to query

Nodes would need to make blockchain RPC calls to verify DIDs, which violates the constraint of no external dependencies. There is no self-contained or self-certifying component - all identity information lives on-chain.

### 2. Ecosystem

**Limited adoption.**

- GitHub repository has only 3 stars and 0 forks
- Created by KayTrust, a relatively small organization focused on self-sovereign identity
- No evidence of widespread tooling or library support outside of KayTrust's own ecosystem
- The specification is marked as "an unofficial draft"
- Limited community activity

### 3. Stability

**Low maturity, limited maintenance.**

- Repository created: October 2021
- Last commit: October 2022 (over 3 years ago)
- Only 5 total commits in the repository
- Specification remains in "unofficial draft" status
- Default branch is named "draft" indicating incomplete status
- No delete/deactivation operation supported

## Special Considerations

- Uses MNID (Multi-Network ID) format which embeds network chain ID, making DIDs network-specific
- Relies on capability-based access control through smart contracts
- Uses EcdsaSecp256k1RecoveryMethod2020 for verification methods
- No deactivation mechanism - DIDs can only be "stopped being used"
- Tied to KayTrust's specific smart contract architecture (Proxy contracts and IdentityManager contracts)

## Recommendation

**No-go**

The did:ev method fundamentally requires access to Ethereum blockchain state to resolve and verify DIDs. All identity information (verification methods, controllers, capabilities) is stored on-chain and must be read via blockchain RPC calls. This directly conflicts with the constraint that nodes cannot make HTTP requests or access external blockchain state.

Additionally, the method has very limited adoption, has not been updated in over 3 years, remains in draft status, and is tightly coupled to a specific smart contract architecture maintained by a single organization. Even if blockchain access were not a constraint, the lack of ecosystem maturity and maintenance would make this a poor choice.
