# did:ti (Tianhe)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/HTiBRI/TiDID-Method-Specification/blob/main/README.md) |
| Organization | Hunan Tianhe Guoyun Technology Co. Ltd |
| DID Format | `did:ti:<base58-ripemd160-sha256-pubkey>` |

## Overview
did:ti is a DID method for the TiChain blockchain, developed by Tianhe Guoyun. The identifier is calculated using Bitcoin-inspired address conversion: base58(ripemd160(sha256(publicKey))). Uses Secp256k1 keys.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires TiChain and Tianhe Cloud infrastructure:

- TiChain blockchain as verifiable data registry
- Tianhe Cloud API (did.tianhecloudapi.com) for all operations
- AK/SK credentials mandatory for all operations
- Cloud account with TiChain node access required
- Updates not supported (documents are immutable)
- Deactivation via DeactivateTiDid endpoint

### 2. Ecosystem
**Very small.** Regional (Chinese) project. Limited documentation and adoption evidence.

### 3. Stability
**Early stage.** Vendor-specific infrastructure. Documentation primarily in Chinese context.

## Recommendation
**No-go**

Requires TiChain blockchain and Tianhe Cloud API access with authentication credentials. Heavily vendor-locked and not suitable for self-contained use.
