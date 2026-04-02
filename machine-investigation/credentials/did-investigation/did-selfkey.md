# did:selfkey (SelfKey)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/SelfKeyFoundation/selfkey-identity/blob/develop/DIDMethodSpecs.md) |
| Organization | SelfKey Foundation |
| DID Format | `did:selfkey:<32-byte-hex-string>` |

## Overview
did:selfkey is a DID method using Ethereum smart contracts for identity management. The identifier is a keccak256 hash of the Ethereum address combined with a nonce. DIDs are registered in the DIDLedger smart contract and controlled by Ethereum addresses.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Ethereum blockchain:

- DID generation: `keccak256(abi.encodePacked(msg.sender, nonce))`
- DIDLedger smart contract stores controller, timestamps, metadata
- Full resolution dynamically constructs documents from on-chain data
- Simple resolution returns controller address via `getController()`
- Optional ERC725 integration for extended functionality

### 2. Ecosystem
**Small to medium.** SelfKey has a focused user base in self-sovereign identity. Reference implementations available. Part of broader Ethereum identity ecosystem.

### 3. Stability
**Moderate.** Well-documented specification. Production deployment on Ethereum mainnet.

## Recommendation
**No-go**

Requires Ethereum blockchain for all DID operations. The smart contract dependency makes it unsuitable for self-contained ledger operation.
