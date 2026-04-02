# did:dsrv (DSRV Labs DID Method)

| Property | Value |
|----------|-------|
| Specification | [DSRV DID Method](https://github.com/dsrvlabs/did_method/blob/main/DID-Method-DSRV.md) |
| Organization | DSRV Labs |
| DID Format | `did:dsrv:<cosmos-address>` |

## Overview

The did:dsrv method enables users to prove ownership of addresses across multiple blockchains through a single DID. It uses mnemonic seed phrases and BIP-44 derivation to generate keys for each blockchain.

Example DID: `did:dsrv:1nyznmezcm7zkjw0glt3y4y4tnhagw6yg00ru87`

### DID Structure

- **Prefix**: `did:dsrv:`
- **Identifier**: Cosmos address (without prefix)

### Key Features

- **Multi-chain derivation**: BIP-44 generates keys for multiple blockchains
- **blockchainAccountId**: Links verification methods to blockchain accounts
- **Cosmos-based**: Uses Cosmos address format
- **No updates**: DID documents cannot be modified

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generated via @dsrv/kms wallet package |
| Read | Only revocation status accessible via blockchain/API |
| Update | **Not supported** |
| Delete | Self-revocation or issuer revocation (permanent) |

### Supported Algorithms

- Secp256k1VerificationKey2018

## Evaluation

### 1. Feasibility/Complexity

**Not directly feasible but has interesting properties**

The did:dsrv method has mixed characteristics:

- **Key derivation**: Standard BIP-44, could work offline
- **Blockchain for revocation**: Only revocation stored on-chain
- **No updates**: Immutable documents simplify verification
- **Cosmos dependency**: Revocation requires Cosmos access

**Adaptation possibility**: Partial. The key derivation is standard, but revocation checking requires Cosmos blockchain access.

### 2. Ecosystem

**Limited / Single vendor**

- **DSRV Labs**: Korean blockchain infrastructure company
- **@dsrv/kms**: Proprietary wallet library
- **Multi-chain focus**: Infrastructure for multiple chains
- **Limited adoption signals**: Small GitHub repository

### 3. Stability

**Maintained**

- **DSRV Labs**: Active company
- **Wallet integration**: Part of DSRV's wallet infrastructure
- **No updates design**: Simplifies long-term stability

## Special Considerations

### Interesting Patterns

- **No update support**: Document immutability simplifies verification
- **Revocation-only on-chain**: Minimal on-chain footprint
- **BIP-44 derivation**: Standard key generation
- **Multi-chain ownership**: Single identity, multiple chains

### Limitations

- **No key rotation**: Cannot update verification methods
- **Proprietary library**: @dsrv/kms dependency
- **Revocation dependency**: Requires Cosmos for revocation check

## Recommendation

**No-go**

The did:dsrv method is unsuitable for our use case:

1. **Cosmos dependency**: Revocation checking requires Cosmos access
2. **Proprietary library**: @dsrv/kms dependency
3. **No updates**: Cannot rotate keys or modify documents
4. **Limited ecosystem**: Single vendor with limited adoption

The "no updates" pattern is interesting for simplicity but the Cosmos revocation dependency makes it impractical.
