# did:ccd (Concordium DID Method)

| Property | Value |
|----------|-------|
| Specification | [Concordium DID Spec](https://proposals.concordium.com/ID/concordium-did.html) |
| Organization | Concordium |
| DID Format | `did:ccd:[network:]<type>:<identifier>` |

## Overview

The did:ccd method is designed for the Concordium blockchain, a layer-1 blockchain with identity integrated into its core protocol. It supports five different DID types for different use cases.

Example DIDs:
- Account: `did:ccd:acc:3kBx2h5Y2veb4hZgAJWPrr8RyQESKm5TjzF3ti1QQ4VSYLwK1G`
- Credential: `did:ccd:cred:b5e231b...` (96-char hex)
- Smart Contract: `did:ccd:sci:123:0`
- Public Key: `did:ccd:pkc:a1b2c3...` (64-char hex)
- Identity Provider: `did:ccd:idp:1`

### DID Types

| Type | Format | Purpose |
|------|--------|---------|
| acc | 50-char base58 | Account-based identity |
| cred | 96-char hex | Credential-based identity |
| sci | index:subindex | Smart contract instance |
| pkc | 64-char hex | Public key cryptography (ephemeral) |
| idp | index | Identity provider |

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Account creation, credential issuance, contract deployment, or key generation |
| Read | gRPC interface queries or concordium-client CLI |
| Update | Credential update transactions, key updates |
| Deactivate | Credential removal through update transactions |

### Supported Algorithms

- Ed25519 (primary)
- ConditionalProof2022 for threshold signatures

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ccd method requires:

- **Concordium blockchain access**: All operations require querying or transacting on Concordium
- **gRPC interface**: Resolution requires Concordium node access
- **Multiple DID types**: Complex implementation across 5 different identifier types
- **Threshold signatures**: Requires ConditionalProof2022 verification method

**Adaptation possibility**: Very low. The method is deeply integrated with Concordium's identity layer and cannot be meaningfully separated from it.

### 2. Ecosystem

**Niche / Single-chain**

- **Concordium-specific**: Only relevant within Concordium ecosystem
- **Enterprise focus**: Designed for regulated identity use cases
- **Limited tooling**: Primarily concordium-client CLI

### 3. Stability

**Stable but draft**

- **Document status**: Draft, subject to updates
- **W3C compliant**: Follows DID Core specification
- **Active development**: Concordium is an active project

## Special Considerations

- **Interesting design**: PKC DIDs are ephemeral and similar to did:key in concept
- **Privacy concerns**: PKC DIDs risk correlation attacks if reused
- **Threshold support**: Multi-signature authentication is well-designed

## Recommendation

**No-go**

The did:ccd method is unsuitable for our use case:

1. **Concordium dependency**: Requires full Concordium node access for all operations
2. **Chain-specific features**: Uses Concordium-specific identity provider and credential systems
3. **No unique benefit**: The interesting parts (like PKC DIDs) are better served by did:key

The PKC DID type is conceptually similar to did:key but adds unnecessary Concordium dependencies.
