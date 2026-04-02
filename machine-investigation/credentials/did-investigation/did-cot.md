# did:cot (Computing of Things DID Method)

| Property | Value |
|----------|-------|
| Specification | [CoTNetwork DID Spec](https://github.com/ComputingOfThings/dids) |
| Organization | ComputingOfThings |
| DID Format | `did:cot:<43-44-char-base58-identifier>` |

## Overview

The did:cot method is built for the CoTNetwork, using BLS signatures and phone number-based linkage for identity verification. Documents are stored on-chain with immutable records.

Example DID: `did:cot:EhXSR8W1fLJnhaYQ3g8BLcszVsDorBs6NV7YfBe4rWgH`

### DID Structure

- **Prefix**: `did:cot:`
- **Identifier**: 43-44 characters, Base58 encoded

### Cryptographic Components

- **Signature suite**: BbsBlsSignature2020
- **Public key type**: Bls12381G2Key2020
- **Linkage**: SHA256 hash of phone number (privacy-preserving)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Submit Verifiable Presentation through UniWa portal |
| Read | Query by DID or linkage hash |
| Update | Resubmit VP with modified document |
| Delete | **Not supported** - immutable records |

### Validation Requirements

1. DID in proof's verification method matches document's verification method
2. Cryptographic proof validates successfully
3. Linkage matches nonce value

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:cot method requires:

- **CoTNetwork infrastructure**: Must use their network
- **UniWa portal**: Registration through specific service
- **Phone verification**: SMS-based identity linkage
- **BLS signatures**: Specific cryptographic scheme

**Adaptation possibility**: Very low. The system is tightly coupled to CoTNetwork infrastructure and phone-based verification.

### 2. Ecosystem

**Niche / Single platform**

- **ComputingOfThings**: Single organization
- **Limited activity**: 5 commits, 2 stars
- **Go implementation**: Written in Go
- **Minimal adoption**: Early stage project

### 3. Stability

**Early stage**

- **Limited documentation**: Status information incomplete
- **Active development**: Recent commits
- **Small team**: Minimal community

## Special Considerations

### Unique Features

- **BLS signatures**: Aggregatable signatures (BbsBlsSignature2020)
- **Phone linkage**: Privacy-preserving phone verification
- **Immutable records**: Cannot delete from chain

### Limitations

- **Phone dependency**: Requires phone verification
- **Platform lock-in**: Only works on CoTNetwork
- **No deletion**: Records are permanent

## Recommendation

**No-go**

The did:cot method is unsuitable for our use case:

1. **CoTNetwork dependency**: Requires their specific infrastructure
2. **Phone verification**: Adds identity verification we don't need
3. **Centralized registration**: Must use UniWa portal
4. **Limited ecosystem**: Minimal adoption and documentation

The BLS signature approach is interesting for aggregation but the overall system is too platform-specific.
