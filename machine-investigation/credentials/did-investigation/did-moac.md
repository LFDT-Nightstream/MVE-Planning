# did:moac (MOAC)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/DavidRicardoWilde/moac-did/blob/master/did-moac-method.md) |
| Organization | MOACChain Foundation |
| DID Format | `did:moac:<40_hex_address>` |

## Overview
MOAC DID is a blockchain-based decentralized identifier method for the MOAC public blockchain. The identifier is a 40-character hexadecimal MOAC blockchain address. DIDs are created from entropy-generated key pairs and registered through smart contract interactions.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The resolution process:
1. Query registry smart contract's `getRecord` function with DID
2. Retrieve corresponding MOAC blockchain address
3. Resolve through smart contract to full DID Document
4. Uses JSON-RPC 2.0 format for responses

All operations depend on MOAC blockchain network (mainnet, testnet, or private chain options).

### 2. Ecosystem
MOAC is a multi-layer blockchain platform designed for high performance. The DID method appears to have limited adoption, with the specification hosted in a personal GitHub repository rather than an official organization.

### 3. Stability
The specification follows W3C standards for document structure. The use of keccak256 for identifier generation and smart contract registry pattern is well-established. However, the project appears to have limited maintenance and community activity.

## Recommendation
**No-go**

Requires MOAC blockchain infrastructure for registration and resolution. Limited ecosystem activity suggests potential sustainability concerns.
