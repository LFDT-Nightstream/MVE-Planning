# did:amo (AMO Blockchain DID Method)

| Property | Value |
|----------|-------|
| Specification | [AMO DID Method Spec](https://github.com/amolabs/docs/blob/master/amo-did.md) |
| Organization | AMO Labs |
| DID Format | `did:amo:<40-char-hex-address>` |

## Overview

The AMO DID method is a decentralized identifier system for the AMO blockchain, a platform specialized in automotive data trading. The DID corresponds directly to an AMO blockchain account address.

Example DID: `did:amo:70EAD5B53B11DFE78EC8CF131D7960F097D48D70`

### DID Structure

- **Prefix**: `did:amo:`
- **Address**: 40 uppercase hexadecimal characters (160-bit account identifier)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Submit `did.claim` transaction with DID and document to AMO blockchain |
| Read | Query `/did` store via ABCI interface or blockchain explorer API |
| Update | Submit new `did.claim` transaction with same DID but modified document |
| Deactivate | Send `did.dismiss` transaction to revoke the DID |

### Supported Algorithms

- **ECDSA over NIST P-256** (secp256r1)
- Public keys represented in JsonWebKey2020 format

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:amo method is entirely dependent on the AMO blockchain:

- **Mainnet only**: Specification explicitly states "valid for AMO blockchain mainnet only"
- **On-chain storage**: DID documents are stored on-chain via `did.claim` transactions
- **Resolution requires blockchain access**: Must query AMO blockchain nodes or explorer API
- **No key rotation**: Specification notes "Currently, there is no mechanism for key rotating and revocation"

**Adaptation possibility**: Very low. Like other chain-specific DID methods, this requires access to AMO blockchain state. The only way to support it would be to:
1. Run AMO blockchain nodes (not practical)
2. Trust a centralized AMO explorer API (defeats decentralization)

### 2. Ecosystem

**Very limited / Niche**

- **Single-purpose blockchain**: AMO is focused specifically on automotive data trading
- **Limited adoption**: Small ecosystem compared to major blockchains
- **No Universal Resolver driver**: Not listed in DIF Universal Resolver
- **Regional focus**: Primary market appears to be automotive data in specific regions
- **Working draft status**: Specification still labeled as "Working Draft"

### 3. Stability

**Uncertain / Early stage**

- **Working draft**: Not a finalized specification
- **No versioning**: No formal version scheme or changelog
- **No key rotation**: Basic feature still missing from specification
- **Blockchain protocol dependency**: References AMO blockchain protocol v6

## Special Considerations

- **Simple format**: Address-based DIDs are straightforward to parse
- **P-256 curve**: Uses NIST P-256 rather than the more common secp256k1 in blockchain space
- **Privacy limitations**: All activities linked to account address (inherent blockchain property)
- **Fixed addresses**: No mechanism for address rotation

## Recommendation

**No-go**

The did:amo method is unsuitable for our use case:

1. **Blockchain dependency**: All operations require AMO blockchain access
2. **Very limited ecosystem**: Niche automotive data platform with minimal adoption
3. **Incomplete specification**: Still a working draft, missing key rotation
4. **No unique value**: Doesn't offer any features that would justify the integration complexity

The P-256 curve choice is interesting (more common in enterprise/government contexts than secp256k1), but this alone doesn't justify supporting a niche blockchain-specific DID method.
