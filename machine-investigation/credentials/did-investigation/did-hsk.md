# did:hsk (HashKey DID)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/hashkeydid/hashkeydid-w3c-specification/blob/master/did-specfication/hsk-method.md) |
| Organization | HashKeyDID |
| DID Format | `did:hsk:<40-char-hex-platon-address>` |

## Overview
The did:hsk method is implemented on the PlatON blockchain network. DIDs are based on PlatON addresses (40 hexadecimal characters). Resolution requires querying the DIDDocument smart contract deployed on PlatON. Supports full CRUD operations with controller-based access control and multiple verification methods.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires direct interaction with the PlatON blockchain by invoking the `resolve` method of the registry smart contract. Clients must query PlatON nodes (public or self-operated) to retrieve DID documents. Uses ECDSA Secp256k1 key pairs.

### 2. Ecosystem
Limited ecosystem tied to the PlatON blockchain community. HashKeyDID appears focused on the Asian market. PlatON itself has moderate adoption but is not among the major blockchain platforms globally.

### 3. Stability
Documented specification with standard DID operations. PlatON is a functioning blockchain with privacy computing features. However, the DID method's adoption and long-term maintenance are uncertain.

## Recommendation
**No-go**

Requires PlatON blockchain access for DID resolution. Node operators cannot verify DIDs without querying PlatON smart contracts.
