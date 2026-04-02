# did:icon (ICON)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/icon-project/icon-DID/blob/master/docs/ICON-DID-method.md) |
| Organization | ICON Foundation |
| DID Format | `did:icon:<network-id>:<idstring+checksum>` |

## Overview
The did:icon method operates on the ICON blockchain, a decentralized network designed to connect independent communities. DIDs are managed by a dedicated smart contract on the blockchain. The identifier combines a 40-character hex string (from transaction hash) with an 8-character checksum derived from SHA3-256. Supports mainnet (network-id "01") and testnets.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution operates entirely on-chain through ICON's smart contract system. Users must submit queries with the DID to retrieve the corresponding document from the blockchain. No external HTTP or DNS dependencies, but requires ICON network access.

### 2. Ecosystem
Established ecosystem within the ICON blockchain community. ICON has been operating since 2017 with focus on interoperability between independent blockchain communities. The DID method supports authentication for persons, organizations, and digital devices.

### 3. Stability
Well-documented specification with clear CRUD operations. ICON is a functioning mainnet with years of operation. Document versioning via block heights provides audit capabilities. Secp256k1 cryptographic support aligns with industry standards.

## Recommendation
**No-go**

Requires ICON blockchain access for DID resolution. Node operators cannot verify DIDs without querying the ICON smart contract on the ICON network.
