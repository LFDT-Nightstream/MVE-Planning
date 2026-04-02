# did:dotbit (.bit DID Method)

| Property | Value |
|----------|-------|
| Specification | [dotbit DID Spec](https://github.com/dotbithq/dotbit-did-spec) |
| Organization | dotbitHQ |
| DID Format | `did:dotbit:<name>.bit` |

## Overview

The did:dotbit method wraps .bit naming accounts (on Nervos blockchain) as interoperable DIDs. .bit is a distributed naming system similar to ENS but on Nervos.

Example DID: `did:dotbit:satoshi.bit`

### DID Structure

- **Prefix**: `did:dotbit:`
- **Identifier**: .bit account name (e.g., `satoshi.bit`)

### Key Features

- **Multi-chain addresses**: Supports Ethereum, Tron, Dogecoin addresses
- **Default verification**: EcdsaSecp256k1RecoveryMethod2020
- **Automatic services**: Web3PublicProfile endpoint generation
- **Custom records**: Three optional fields for customization

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Register .bit account via Nervos blockchain |
| Read | Retrieve .bit data, apply DID records, generate defaults |
| Update | Modify custom records per .bit data container spec |
| Deactivate | Annual fee expiration triggers deactivation |

### Deactivation Note

Account expiration enables re-registration by other entities (identifier recycling).

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dotbit method requires:

- **Nervos blockchain**: .bit runs on Nervos (CKB)
- **.bit infrastructure**: Requires .bit naming system access
- **Annual fees**: Accounts require ongoing payment
- **Name registration**: Must register through .bit system

**Adaptation possibility**: None. This is a wrapper around .bit naming system which itself requires Nervos blockchain access.

### 2. Ecosystem

**Niche / Nervos ecosystem**

- **dotbitHQ**: Maintains specification
- **Nervos integration**: Part of CKB ecosystem
- **Early stage**: 5 commits, 1 star
- **Naming focus**: Similar to ENS but smaller

### 3. Stability

**Early stage**

- **Minimal activity**: Very limited GitHub engagement
- **Specification complete**: Basic documentation exists
- **Nervos dependency**: Tied to Nervos/CKB success

## Special Considerations

### Name-based Identity

- Human-readable names (like ENS)
- Account expiration and recycling
- Multi-chain address support

### Limitations

- Nervos blockchain required
- Annual fee model
- Identifier recycling risk

## Recommendation

**No-go**

The did:dotbit method is unsuitable for our use case:

1. **Nervos dependency**: Requires Nervos blockchain access
2. **.bit infrastructure**: Tied to .bit naming system
3. **External chain state**: Cannot verify from our ledger
4. **Limited ecosystem**: Minimal adoption outside Nervos

This is essentially a DID wrapper around a blockchain naming system, with the same issues as other chain-specific methods.
