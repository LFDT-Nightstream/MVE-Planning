# did:ens (ENS DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:ens Spec](https://github.com/veramolabs/did-ens-spec) |
| Organization | Veramolabs (ConsenSys MESH) |
| DID Format | `did:ens:[network:]<ens-name>` |
| Status | **Work in Progress - v0.1.1** |

## Overview

The did:ens method wraps Ethereum Name Service (ENS) names as interoperable DIDs. It maps ENS names to DID documents by retrieving owner information and optional TEXT records.

Example DIDs:
- `did:ens:some.eth`
- `did:ens:mainnet:some.eth`

### DID Structure

- **Prefix**: `did:ens:`
- **Network** (optional): mainnet, rinkeby, ropsten, goerli
- **Name**: ENS name (e.g., `vitalik.eth`)

### Key Features

- **ENS integration**: Wraps existing ENS names
- **Default verification**: Owner address as EcdsaSecp256k1RecoveryMethod2020
- **Custom records**: Optional TEXT records for services/methods
- **Web3PublicProfile**: Automatic service endpoint

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Standard ENS registration process |
| Read | ENS lookup + TEXT record retrieval |
| Update | Modify ENS TEXT records |
| Delete | ENS expiration or transfer |

### TEXT Records for Customization

- `org.w3c.did.service`
- `org.w3c.did.verificationMethod`
- `org.w3c.did.verificationRelationship`

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ens method has the same issues as did:ethr:

- **Ethereum dependency**: Requires Ethereum network access
- **ENS resolution**: Cannot resolve ENS from our ledger
- **On-chain data**: TEXT records stored on Ethereum
- **Name ownership**: Identity tied to ENS name control

**Adaptation possibility**: None. ENS resolution requires Ethereum access.

### 2. Ecosystem

**Active but dependent**

- **Veramolabs/ConsenSys**: Strong backing
- **ENS ecosystem**: Large existing user base
- **Work in progress**: Not production ready
- **Ethereum-tied**: Limited to Ethereum ecosystem

### 3. Stability

**Work in progress**

- **v0.1.1**: Early version
- **6 open issues**: Active development
- **Not production ready**: Explicit warning in spec

## Special Considerations

### Privacy Concerns

- ENS names may be personally identifying
- Key transfer when ENS ownership changes
- Transaction history exposed via account

### Name Recycling Risk

ENS names can expire and be re-registered by others, creating identity confusion.

## Recommendation

**No-go**

The did:ens method is unsuitable for our use case:

1. **Ethereum dependency**: Same issue as all EVM methods
2. **ENS resolution required**: Cannot verify from our ledger
3. **Work in progress**: Not production ready
4. **Name expiration risk**: Identity tied to renewable name

This is essentially did:ethr with ENS name wrapping - same fundamental limitations.
