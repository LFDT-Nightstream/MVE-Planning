# did:ttm (Token.TM)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/TokenTM/TM-DID/blob/master/docs/en/DID_spec.md) |
| Organization | Token.TM |
| DID Format | `did:ttm:<32-byte-hex-string>` |

## Overview
did:ttm is a DID method for the TMChain network (Ethereum-compatible). The identifier is generated via keccak256 hash of sender address plus nonce, enabling multiple DIDs per address while maintaining randomness. Uses secp256k1 cryptography.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires TMChain blockchain:

- TM_DID smart contract on TMChain (Ethereum-compatible)
- DID generation: `keccak256(abi.encodePacked(msg.sender, nonce))`
- Resolution via `getDID(address)` contract method
- Returns owner, timestamps, revocation status
- All CRUD operations as blockchain transactions

### 2. Ecosystem
**Small.** Focused on Token.TM platform. Limited documentation and adoption evidence.

### 3. Stability
**Early to moderate.** Follows W3C DID standards. Simple Ethereum-compatible design.

## Recommendation
**No-go**

Requires TMChain blockchain for all operations. Cannot function without access to TMChain network.
