# did:dock (Dock Network DID Method)

| Property | Value |
|----------|-------|
| Specification | [Dock DID Method Spec](https://github.com/docknetwork/dock-did-driver/blob/master/Dock%20DID%20method%20specification.md) |
| Organization | Dock Network |
| DID Format | `did:dock:<SS58-address>` |

## Overview

The did:dock method is built on Dock Network, a permissionless blockchain based on Substrate. It stores DID keys, controllers, and service endpoints on-chain, with the full DID document constructed at resolution time.

Example DID: `did:dock:5CqJC...` (SS58 encoded address)

### DID Structure

- **Prefix**: `did:dock:`
- **Identifier**: SS58 format (base58 + checksum)
- **On-chain storage**: 32 raw bytes (without prefix)

### Key Features

- **Creator vs Owner**: DID creator is not necessarily the owner
- **Partial on-chain**: Only keys, controllers, service endpoints stored
- **Token-based transactions**: Requires Dock tokens for operations
- **Anonymous transactions**: Transaction sender can differ from DID owner

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Register with one or more public keys |
| Read | Query chain, construct DID document from stored components |
| Update | Add/remove keys, controllers, endpoints via signed messages |
| Delete | Remove DID through authorized key signatures |

### Supported Algorithms

| Algorithm | Key Size | Signature Size |
|-----------|----------|----------------|
| Sr25519 (Schnorr) | 32 bytes | 64 bytes |
| Ed25519 | 32 bytes | 64 bytes |
| secp256k1 | 33 bytes | 65 bytes |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dock method requires:

- **Dock blockchain access**: Must query Dock network for resolution
- **Substrate/Polkadot ecosystem**: Built on Substrate framework
- **Token economics**: Requires DOCK tokens for transactions
- **Proof of stake**: Dock is a PoS chain

**Adaptation possibility**: Low. While Substrate is well-documented, we cannot verify Dock chain state without running Dock nodes.

### 2. Ecosystem

**Active / Substrate ecosystem**

- **Dock Network**: Active company with VC funding
- **Substrate-based**: Part of Polkadot ecosystem
- **Production use**: Real deployments
- **SDK available**: Developer tools provided

### 3. Stability

**Production ready**

- **Mainnet live**: Dock blockchain operational
- **Corporate backing**: Dock.io maintains
- **Active development**: Regular updates

## Special Considerations

### Interesting Patterns

- **Partial on-chain storage**: Only essential data on-chain
- **Anonymous transactions**: Sender can be separate from DID owner
- **Multi-key registration**: Multiple keys at creation
- **Substrate flexibility**: Well-designed key management

### Privacy Notes

- Recommends different DIDs to avoid correlation
- Transaction logs may reveal account connections

## Recommendation

**No-go**

The did:dock method is unsuitable for our use case:

1. **Dock blockchain dependency**: Requires Dock network access
2. **Token requirements**: Needs DOCK tokens for operations
3. **External chain state**: Cannot verify from our ledger
4. **Substrate-specific**: Tied to Substrate/Polkadot ecosystem

The partial on-chain storage pattern and key management approach are well-designed but require Dock infrastructure.
