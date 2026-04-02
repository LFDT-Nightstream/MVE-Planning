# did:echo (ECHO Protocol DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:echo Spec](https://github.com/echoprotocol/uni-resolver-driver-did-echo/blob/master/echo_did_specifications.md) |
| Organization | Pixelplex / ECHO Protocol |
| DID Format | `did:echo:<network>.<object-id>` |

## Overview

The did:echo method creates DIDs as special objects within the ECHO network. Documents are stored within blockchain objects using Ed25519 verification keys.

Example DIDs:
- `did:echo:0.1.25.2` (mainnet)
- `did:echo:1.1.25.1` (testnet)
- `did:echo:2.1.25.0` (devnet)

### DID Structure

- **Prefix**: `did:echo:`
- **Network**: 0 (mainnet), 1 (testnet), 2 (devnet), 255 (unknown)
- **Object ID**: Three digit groups separated by periods

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | `did_create_operation` with registrar, public keys, fee |
| Read | `get_did_object` JSON-RPC query |
| Update | `did_update_operation` to add/remove keys |
| Delete | `did_delete_operation` to revoke DID |

### Supported Algorithms

- Ed25519 (Base58 encoded)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:echo method requires:

- **ECHO network**: All operations require ECHO protocol access
- **ECHO wallet**: Transaction construction via ECHO libraries
- **Network Broadcast API**: Transaction submission
- **Object-based storage**: ECHO-specific data model

**Adaptation possibility**: Very low. Tightly coupled to ECHO protocol.

### 2. Ecosystem

**Minimal / Single platform**

- **Pixelplex**: Development company
- **ECHO protocol**: Limited adoption blockchain
- **Incomplete spec**: TODO sections remaining
- **Minimal engagement**: 0 stars, 1 fork

### 3. Stability

**Early stage / Incomplete**

- **March 2020**: Dated specification
- **TODO sections**: Security/performance incomplete
- **Minimal activity**: Limited development

## Recommendation

**No-go**

The did:echo method is unsuitable for our use case:

1. **ECHO protocol dependency**: Requires ECHO network access
2. **Incomplete specification**: TODO sections remain
3. **Minimal ecosystem**: Very limited adoption
4. **Object-based model**: ECHO-specific architecture
