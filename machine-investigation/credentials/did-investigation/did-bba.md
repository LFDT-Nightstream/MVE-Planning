# did:bba (Ardor Blockchain DID Method)

| Property | Value |
|----------|-------|
| Specification | [BBA DID Method Spec](https://github.com/blobaa/bba-did-method-specification) |
| Organization | blobaa.dev |
| DID Format | `did:bba:[network:]<transaction-hash>` |

## Overview

The did:bba method uses the Ardor blockchain (specifically the IGNIS child chain) as a decentralized public-key infrastructure (DPKI) for managing DIDs. It leverages Ardor's Account Properties and Data Cloud features for attestation and document storage.

Example DID: `did:bba:t:45e6df15dc0a7d91dcccd24fda3b52c3983a214fb0eed0938321c11ec99403cf`

### DID Structure

- **Prefix**: `did:bba:`
- **Network** (optional): `m` (mainnet) or `t` (testnet) - mainnet assumed if omitted
- **Transaction hash**: 64-character hexadecimal string (attestation transaction)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Store DDOT via Ardor Cloud Storage, create attestation account property |
| Read | Retrieve attestation, follow delegation chain, fetch DDOT, merge into document |
| Update | Store new DDOT and update attestation reference, or transfer control via delegation |
| Deactivate | Set attestation state to "i" (inactive) - permanent and irreversible |

### Supported Algorithms

- Curve25519 (Ardor native)
- Security relies on Ardor's transaction signing

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:bba method is tightly coupled to Ardor blockchain:

- **IGNIS child chain**: Requires Ardor/IGNIS infrastructure
- **Account Properties**: Uses Ardor-specific feature for attestations
- **Data Cloud storage**: DDOT stored via Ardor's cloud storage mechanism
- **Transaction-based identity**: DIDs are derived from transaction hashes

**Adaptation possibility**: Very low. The method relies entirely on Ardor-specific features. We cannot verify Ardor state without running Ardor nodes.

### 2. Ecosystem

**Small / Niche**

- **Ardor/NXT ecosystem**: Limited to users of this specific blockchain
- **Single developer**: Maintained by individual developer (blobaa)
- **Low adoption**: Ardor has a relatively small user base
- **Self-referential**: Primarily serves Ardor ecosystem needs

### 3. Stability

**Stable but niche**

- **Well-documented**: Comprehensive specification
- **Mature design**: Clear separation of controller from DID keys
- **Single maintainer risk**: Dependent on one developer
- **Ardor stability**: Tied to Ardor blockchain's continued operation

## Special Considerations

- **Controller separation**: DID controller (Ardor account) is separate from keys in DID document
- **Delegation chain**: Supports transferring control through attestation delegation
- **Permanent deactivation**: Cannot reactivate once deactivated
- **Lightweight attestations**: Only 160 characters in Account Properties

## Recommendation

**No-go**

The did:bba method is unsuitable for our use case:

1. **Ardor dependency**: Requires Ardor/IGNIS blockchain access for all operations
2. **Small ecosystem**: Limited adoption and single-developer maintenance
3. **No unique value**: Doesn't offer features that justify the integration complexity

The design pattern of separating DID controller from document keys is interesting and could inform our own design, but the Ardor-specific implementation makes direct adoption impractical.
