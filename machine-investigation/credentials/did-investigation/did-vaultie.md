# did:vaultie (Vaultie)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/vaultie/vaultie-did-method/blob/master/vaultie-did-method-specification.md) |
| Organization | Vaultie Inc. |
| DID Format | `did:vaultie:<ethereum-address>` |

## Overview
did:vaultie is a DID method that uses Ethereum addresses as identifiers and IPFS for DID document storage. The method avoids smart contracts by using Ethereum transaction input data for DID-to-IPFS mappings. Resolution involves querying Ethereum for self-directed transactions containing IPFS hashes, then retrieving DID documents from IPFS.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires two external infrastructure systems:
- **Ethereum** (or EVM-compatible networks) for anchoring signatures and transaction history
- **IPFS** for storing and retrieving DID documents
- Resolution process:
  1. Query Ethereum for transactions from the DID address to itself
  2. Filter transactions by Keccak-256 hash prefix of the DID
  3. Extract IPFS hash from transaction input data
  4. Convert hex to Base58 and fetch document from IPFS

### 2. Ecosystem
Small ecosystem. The specification is authored by Dmitry Semenovskiy at Vaultie Inc. Uses standard Ethereum/IPFS stack, which has broader ecosystem support. The dual-dependency (Ethereum + IPFS) adds complexity.

### 3. Stability
The specification is documented on GitHub. Conforms to W3C standards. The approach is technically sound but relies on continued availability of both Ethereum and IPFS infrastructure.

## Recommendation
**No-go**

Requires both Ethereum blockchain and IPFS infrastructure for resolution. Double external dependency makes this unsuitable for self-contained operation.
