# did:emtrust (Emtrust WAI DID Method)

| Property | Value |
|----------|-------|
| Specification | [Emtrust Spec](https://github.com/Halialabs/did-spec/blob/gh-pages/readme.md) |
| Organization | Halialabs |
| DID Format | `did:emtrust:<40-char-hex-id>` |

## Overview

Emtrust WAI is a distributed identity system implementing W3C and DIF standards for self-sovereign identity. It uses Hyperledger Fabric as its blockchain and IPFS-based storage with encryption layers.

Example DID: `did:emtrust:0xdadac9f39033a7205b28849cc1dae698d5ceac18`

### DID Structure

- **Prefix**: `did:emtrust:`
- **Identifier**: 40 characters from double-hashing public key

### Infrastructure

- **Hyperledger Fabric**: Permissioned blockchain
- **IPFS**: Encrypted object storage via Halialabs layer
- **Identity channel**: Shared among participating organizations
- **Service provider APIs**: Resolution endpoints

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate entropy → key pair → hash → enroll with service provider |
| Read | Query blockchain smart contract via service provider APIs |
| Update | Controlled by organizational moderators |
| Delete | **Not supported** - revocation via intent assertion only |

### Key Management

- Private keys remain on user devices
- 32-bit seed phrases for recovery
- Recovery events logged on-chain
- Service providers handle revocation assertions

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:emtrust method requires:

- **Hyperledger Fabric**: Permissioned blockchain network
- **Organizational membership**: Must be part of identity channel
- **Service provider APIs**: Cannot self-resolve
- **Halialabs infrastructure**: Proprietary storage layer

**Adaptation possibility**: Very low. Permissioned model and proprietary infrastructure.

### 2. Ecosystem

**Limited / Enterprise-focused**

- **Halialabs**: Single vendor
- **v0.1**: Early stage specification
- **Permissioned model**: Requires organization participation
- **Limited visibility**: Minimal public adoption

### 3. Stability

**Early stage**

- **v0.1**: Early specification
- **Enterprise focus**: Not general-purpose
- **Limited documentation**: Minimal details

## Recommendation

**No-go**

The did:emtrust method is unsuitable for our use case:

1. **Hyperledger Fabric dependency**: Requires specific permissioned blockchain
2. **Organizational model**: Not permissionless
3. **Service provider dependency**: Cannot self-verify
4. **No deletion**: Only revocation assertions supported

The permissioned model fundamentally conflicts with our requirements.
