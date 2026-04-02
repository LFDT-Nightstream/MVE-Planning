# did:evan (evan.network DID Method)

| Property | Value |
|----------|-------|
| Specification | [evan.network DID Spec](https://github.com/evannetwork/evan.network-DID-method-specification/blob/master/evan_did_method_spec.md) |
| Organization | evan.network |
| DID Format | `did:evan:[network:]<account-or-asset-id>` |

## Overview

The did:evan method is designed for evan.network, a blockchain for digitalization and automation of business transactions. It supports both self-sovereign identities (accounts) and asset identities (digital twins).

Example DIDs:
- Account: `did:evan:testcore:0x0d87204c3957d73b68ae28d0af961d3c72403906`
- Asset: `did:evan:0xd2537e2ec4a554b1b1d224fe42a81bd7a571e307e3a10863ca447fba261fc388`

### DID Structure

- **Prefix**: `did:evan:`
- **Network** (optional): `core` or `testcore` (default: core)
- **Identifier**: 20-byte (account) or 32-byte (asset) hex string

### Identity Types

| Type | Description |
|------|-------------|
| Self-Sovereign Identity (SSI) | Created by individuals via private keys |
| Asset Identity | Digital twins owned by SSIs |
| External Reference | Skeleton identities for external resources |

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate keys (SSI) or create asset |
| Read | Query smart contract or REST endpoint |
| Update | Modify controllers, delegates, service endpoints |
| Deactivate | Via evan.network API |

### Supported Algorithms

- Secp256k1

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:evan method requires:

- **evan.network blockchain**: Ethereum-compatible but separate network
- **Smart contract registry**: DID documents in contracts
- **REST endpoints**: Resolution via DID resolver smart agent
- **Digital twin focus**: Designed for IoT/enterprise

**Adaptation possibility**: Very low. Tied to evan.network infrastructure.

### 2. Ecosystem

**Enterprise / Niche**

- **evan.network**: German enterprise blockchain
- **Digital twins**: IoT and machine identity focus
- **v0.9**: Mature specification
- **Enterprise adoption**: B2B use cases

### 3. Stability

**Mature but dated**

- **December 2019**: Last major update
- **v0.9**: Near-final version
- **Work in progress sections**: Security/privacy incomplete

## Special Considerations

### Interesting Features

- **Asset identities**: Digital twins for machines/products
- **Delegation**: Owner delegation to SSIs
- **External references**: Bridge to external resources

### Privacy Notes

- Random ID generation for pseudonymity
- DID documents publicly resolvable
- Not anonymous, but pseudonymous

## Recommendation

**No-go**

The did:evan method is unsuitable for our use case:

1. **evan.network dependency**: Requires specific blockchain
2. **Enterprise focus**: Designed for B2B/IoT scenarios
3. **External chain state**: Cannot verify from our ledger
4. **Dated specification**: Limited recent activity

The digital twin pattern is interesting but the infrastructure requirements are prohibitive.
