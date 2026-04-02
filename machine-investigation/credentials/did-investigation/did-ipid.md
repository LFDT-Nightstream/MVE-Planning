# did:ipid (IPFS)

| Property | Value |
|----------|-------|
| Specification | [IPID DID Method](https://did-ipid.github.io/ipid-did-method/) |
| Organization | Community (jonnycrunch as editor) |
| DID Format | `did:ipid:<multihash>` |

## Overview
IPID is a DID method built on IPFS (InterPlanetary File System) that uses the cryptographic multihash of an IPFS node's public key as the identifier. Resolution occurs via IPNS (InterPlanetary Name System) without requiring blockchain infrastructure. The method implements a "micro-ledger" approach with linked version history.

## Evaluation

### 1. Feasibility/Complexity
**Requires external infrastructure but not blockchain.** While explicitly stated as "not a blockchain solution," IPID depends on:
- IPFS/IPNS infrastructure for content addressing and mutable naming
- Libp2p peer-to-peer protocol layer
- Public gateways (ipfs.io, Cloudflare, Infura) for content retrieval

The method could potentially work with local IPFS nodes, but practical resolution typically requires network connectivity to the IPFS swarm or gateways.

### 2. Ecosystem
IPFS has a large and active ecosystem. However, the IPID DID method itself appears to have limited adoption. The specification dates from December 2018 and may not have seen significant updates.

### 3. Stability
Draft specification from 2018. The reliance on IPFS infrastructure is stable, but the DID method specification itself may be dated. Issues noted in the specification regarding JSON-LD @context handling suggest incomplete standardization.

## Recommendation
**No-go**

While not blockchain-dependent, the method requires IPFS/IPNS network infrastructure and public gateways for practical resolution. Cannot function in a fully self-contained manner.
