# did:io (IoTeX)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/iotexproject/iotex-did/blob/master/README.md) |
| Organization | IoTeX Project |
| DID Format | `did:io:<IoTeX_account_address>` |

## Overview
IoTeX DID is a decentralized identifier method designed for the IoTeX blockchain ecosystem, targeting IoT device identity and user authentication. DIDs are registered via smart contracts on the IoTeX blockchain, with DID documents stored off-chain (user-selectable storage) while only hashes and URIs are maintained on-chain.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method is fundamentally dependent on the IoTeX blockchain for DID registration, resolution, and verification. Smart contracts implementing the `SelfManagedDID` interface are required for all CRUD operations. Resolution requires querying the IoTeX blockchain to retrieve document hashes and URIs.

### 2. Ecosystem
Moderate ecosystem focused on IoT applications. IoTeX is an established blockchain project with active development, but the DID implementation appears to have limited adoption outside the IoTeX-specific use cases.

### 3. Stability
The specification is documented but appears to be project-specific rather than following a formal standardization process. The reliance on Solidity smart contracts suggests technical maturity within its ecosystem.

## Recommendation
**No-go**

Requires the IoTeX blockchain infrastructure for all operations. Cannot function as a self-contained DID method without external blockchain dependencies.
