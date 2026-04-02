# did:iota (IOTA)

| Property | Value |
|----------|-------|
| Specification | [IOTA Docs](https://docs.iota.org/developer/iota-identity/references/iota-did-method-spec) |
| Organization | IOTA Foundation |
| DID Format | `did:iota:<network>:<tag>` |

## Overview
IOTA DID is a decentralized identifier method built on the IOTA distributed ledger (Tangle). The method stores DID documents directly on the IOTA network using Alias Outputs (v1.0) or Object IDs (v2.0). It provides W3C-compliant DIDs and Verifiable Credentials for self-sovereign identity without central authorities.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** Resolution requires:
1. Extracting the network and tag from the DID
2. Querying an IOTA node running the indexer
3. Retrieving and parsing the Identity object from the Tangle
4. Decoding the DID document from its packed encoding

The method is tightly coupled to the IOTA Tangle infrastructure. Recent updates (IOTA Rebased in 2024) have changed the underlying protocol, requiring specific node implementations.

### 2. Ecosystem
Strong ecosystem with active development. The IOTA Identity framework has seen significant updates including:
- Selective disclosure via SD-JWT format
- StatusList2021 revocation support
- Experimental gRPC service for language-agnostic integration

IOTA has enterprise adoption focus and active community development.

### 3. Stability
The specification is well-documented with two major versions (v1.0 and v2.0). The transition to IOTA Rebased (approved December 2024) represents ongoing evolution of the underlying platform, which may affect long-term stability.

## Recommendation
**No-go**

Requires the IOTA Tangle (distributed ledger) for all DID operations. Cannot function independently without IOTA network infrastructure.
