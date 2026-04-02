# did:dweb (DWEB DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:dweb Spec](https://github.com/Aisino-Blockchain/dweb-Method-Specification) |
| Organization | Aisino-Blockchain |
| DID Format | `did:dweb:<namespace>:<identifier>` |

## Overview

The did:dweb method is a blockchain-based DID system where documents are generated via smart contracts. It's a draft specification from Aisino-Blockchain.

Example DID: `did:dweb:dtds-1:4WERTYKJHGFC4569RFB38E719nmDSAk961Kr`

### DID Structure

- **Prefix**: `did:dweb:`
- **Namespace ID**: Less than 10 characters (lowercase letters/numbers)
- **Identifier**: Base32-like encoding (custom character set)

### Key Features

- **Smart contract based**: DID documents from contracts
- **Immutable documents**: Updates not supported
- **Secp256k1**: Standard verification method
- **Nonce validation**: Replay attack prevention

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Submit DID, public key, signature; returns boolean |
| Read | Query by DID; returns complete document |
| Update | **Not supported** (immutable design) |
| Delete | Submit DID and signature; removes from ledger |

### Security Features

- Nonce-based replay prevention
- DID controller signature authorization
- MITM protection via genesis block verification
- DoS mitigation through decentralized architecture

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dweb method requires:

- **Specific blockchain**: Aisino's blockchain infrastructure
- **Smart contracts**: Document generation via contracts
- **DWEB agents**: Private keys hosted in DWEB agents
- **No updates**: Immutable document design

**Adaptation possibility**: Very low. The system is tied to Aisino's blockchain infrastructure and agent architecture.

### 2. Ecosystem

**Minimal / Chinese origin**

- **Aisino-Blockchain**: Chinese organization
- **Draft status**: Not finalized
- **No adoption signals**: 0 stars, 0 forks
- **No releases**: Early development stage

### 3. Stability

**Draft / Early stage**

- **Explicit draft**: "Will be updated based on W3C"
- **Residual risks acknowledged**: Cryptographic and implementation risks
- **Minimal activity**: Very limited development

## Special Considerations

### Design Choices

- **Immutable documents**: Simplifies verification but limits flexibility
- **Namespace separation**: Multiple namespaces supported
- **Custom encoding**: Non-standard Base32 character set

### Limitations

- No update support
- Proprietary blockchain dependency
- Limited documentation

## Recommendation

**No-go**

The did:dweb method is unsuitable for our use case:

1. **Aisino blockchain dependency**: Requires their infrastructure
2. **No updates**: Immutable documents limit flexibility
3. **Minimal ecosystem**: No adoption or community
4. **Draft status**: Specification not stable

The immutable document design is interesting for simplicity but the proprietary blockchain dependency is a blocker.
