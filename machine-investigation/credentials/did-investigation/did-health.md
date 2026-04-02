# did:health (DID Health)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/didhealth/health-did-resolver/blob/main/doc/did-method-spec.md) |
| Organization | DID Health Core Team |
| DID Format | `did:health:[network:]<0x-ethereum-address-or-pubkey>` |

## Overview
The did:health method is built on Ethereum's ERC1056 registry standard, designed for healthcare identity management. DIDs can be either Ethereum addresses (40 hex chars) or compressed secp256k1 public keys (66 hex chars). Resolution reconstructs DID documents by querying blockchain events (DIDOwnerChanged, DIDDelegateChanged, DIDAttributeChanged). Supports mainnet and various testnets.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires access to an Ethereum network (mainnet or testnet) to query the ERC1056 smart contract. While no additional HTTP/DNS infrastructure is needed beyond blockchain access, node operators must still connect to Ethereum nodes to enumerate contract events and reconstruct DID documents.

### 2. Ecosystem
Moderate ecosystem leveraging the well-established ERC1056 (Ethr-DID) pattern. Healthcare-focused branding but technically compatible with broader Ethereum DID infrastructure. Benefits from Ethereum's large developer community and tooling.

### 3. Stability
Based on mature ERC1056 standard with established implementations. Supports meta-transactions for third-party gas payment. Well-documented with clear versioning support via query parameters.

## Recommendation
**No-go**

Requires Ethereum blockchain access for DID resolution. Node operators cannot verify DIDs without querying the ERC1056 smart contract on an Ethereum network.
