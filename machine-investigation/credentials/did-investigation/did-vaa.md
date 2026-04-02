# did:vaa (VAA / BIF)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/caict-develop-zhangbo/vaa-method/blob/master/README.md) |
| Organization | China Academy of Information and Communications Technology (CAICT) |
| DID Format | `did:vaa:gxAcE6upFw6DNbG3rfngM3VxCNB` |

## Overview
did:vaa is a DID method built on the Blockchain Identifier Infrastructure (BIF), a permissioned public blockchain developed by CAICT in China. The method uses ECC-generated public keys with Base58 encoding (27-31 characters). CAICT holds the "VAA" Issuing Agency Code from ISO/IEC 15459. The method also supports smart contract identifiers with an alternative format.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires the BIF blockchain infrastructure:
- Resolution via HTTP queries to BIF blockchain nodes
- CRUD operations through POST/GET requests to the BIF network
- Governed by Chinese government-affiliated organization (CAICT)
- Available at http://bidspace.cn/
- Uses ECC cryptography with specific key derivation process

### 2. Ecosystem
Government-backed ecosystem in China. CAICT is a major telecommunications research institution under China's Ministry of Industry and Information Technology. The BIF blockchain is designed for distributed trust management. Limited international adoption.

### 3. Stability
The specification follows W3C DID Core standards. Backed by a significant Chinese government institution, providing institutional stability within China. However, geopolitical and accessibility concerns may limit broader adoption.

## Recommendation
**No-go**

Requires the BIF (Blockchain Identifier Infrastructure) network operated by CAICT in China. Cannot function without access to this specific blockchain infrastructure.
