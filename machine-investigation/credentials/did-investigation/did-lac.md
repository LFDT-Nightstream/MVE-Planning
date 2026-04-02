# did:lac (LACChain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/lacchain/lacchain-did-registry/blob/master/DID_SPEC.md) |
| Organization | LACChain (IDB Lab / BID Lab) |
| DID Format | `did:lac:[network:]<ethereum_address>` |

## Overview
LACChain DID is an Ethereum-based decentralized identifier method for the LACChain network, targeting Latin American blockchain adoption. The method extends the ERC-1056 standard with enhanced features including multiple controller support and automatic key rotation.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** Resolution requires:
1. Query DID Registry contract using `eth_call changed(address)`
2. Retrieve latest block where changes occurred
3. Filter contract events (`DIDAttributeChanged`, controller changes) backwards
4. Reconstruct complete DID document from event history

The method is fundamentally dependent on Ethereum-based smart contracts and blockchain state.

### 2. Ecosystem
LACChain is backed by the Inter-American Development Bank (IDB Lab) with focus on Latin American adoption. The network has institutional support and government partnerships in the region.

### 3. Stability
The specification extends ERC-1056 (a well-established Ethereum identity standard) with additional features:
- Multiple controller support
- Per-verification-method controller assignment
- Automatic key rotation (configurable)
- On-chain key recovery mechanism

The institutional backing provides stability assurance.

## Recommendation
**No-go**

Requires Ethereum-compatible blockchain infrastructure (LACChain network). Cannot function without smart contract registry and blockchain access for resolution.
