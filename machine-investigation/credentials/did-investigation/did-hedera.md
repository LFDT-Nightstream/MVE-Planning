# did:hedera (Hedera Hashgraph)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/hashgraph/did-method) |
| Organization | Hedera Hashgraph / Swirlds |
| DID Format | `did:hedera:<network>:<identifier>` |

## Overview
The did:hedera method uses the Hedera Consensus Service (HCS) for DID CRUD operations. Hedera Hashgraph is a public distributed ledger using hashgraph consensus (Asynchronous Byzantine Fault Tolerant). DID documents can be resolved either by constructing elements from HCS messages or by looking up documents from IPFS via CID references registered through HCS. The method supports both Java and JavaScript SDKs.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires interaction with the Hedera network to read HCS messages. For IPFS-based resolution, additional external network access to IPFS is required. The system uses proof-of-stake consensus with HBAR tokens for transaction fees, requiring economic participation in the network.

### 2. Ecosystem
Substantial ecosystem backed by Hedera's enterprise governance council (major corporations). Active development with HIP-27 improvements for W3C DID Core 1.0 alignment. Integration with DIF Universal Resolver project. Strong enterprise adoption in financial services and supply chain.

### 3. Stability
Mature specification aligned with W3C DID Core 1.0. Hedera mainnet is production-ready with high throughput and finality guarantees. Well-maintained SDKs and documentation. Strong corporate governance provides stability but also centralization concerns.

## Recommendation
**No-go**

Requires access to Hedera Hashgraph network (and potentially IPFS) for DID resolution. Node operators cannot verify DIDs without connecting to Hedera's distributed ledger infrastructure.
