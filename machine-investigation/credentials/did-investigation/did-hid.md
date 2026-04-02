# did:hid (Hypersign)

| Property | Value |
|----------|-------|
| Specification | [HIP-10](https://github.com/hypersign-protocol/HIPs/blob/main/HIPs/hip-10.md) |
| Organization | Hypersign Protocol / Hypermine |
| DID Format | `did:hid:<caip10-address>` or `did:hid:<alphanumeric-id>` |

## Overview
The did:hid method operates on the Hypersign Identity Network, a permissionless blockchain built on Cosmos-SDK for managing digital identity. It supports two identifier formats: CAIP-10 blockchain account format (e.g., `did:hid:eip155:1:0x...`) and alphanumeric format. Documents are stored on-chain and retrieved via gRPC or API calls. Supports IBC module for cross-blockchain queries within the Cosmos ecosystem.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution operates on-chain through the Hypersign blockchain, requiring gRPC or API access to retrieve DID documents. While it eliminates external HTTP/DNS dependencies for core resolution, it still requires connection to the Hypersign network nodes.

### 2. Ecosystem
Growing ecosystem within the Cosmos/IBC community. Supports Web3 reputation systems and on-chain KYC credentials. Multi-signature support and multiple verification methods (Ed25519, EcdsaSecp256k1) provide flexibility. Active development with Hypermine as the backing organization.

### 3. Stability
Specification documented as HIP-10 (Hypersign Improvement Proposal). Built on mature Cosmos-SDK foundation. W3C DID Core compliant. However, the Hypersign network itself is relatively new compared to established blockchains.

## Recommendation
**No-go**

Requires access to the Hypersign Identity Network (Cosmos-based blockchain) for DID resolution. Node operators cannot verify DIDs without querying the Hypersign blockchain via gRPC or API.
