# did:holo (Holochain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/Holo-Host/did-holo-resolver/blob/master/doc/did_holo_spec.md) |
| Organization | Holo-Host / Holochain |
| DID Format | `did:holo:[network:]<hex-address>` |

## Overview
The did:holo method is designed for Holochain, a lightweight peer-to-peer framework that uses a distributed hash table (DHT) combined with local data storage instead of traditional blockchain consensus. DIDs are linked to user keys, and all transactions are signed. The method offers better performance than traditional blockchains (writes accepted in under 2 seconds).

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution requires setting up a local DeepKey instance and making API calls to a Holochain conductor. Creating and managing keys requires running a Holochain source chain for proper key validation. The DHT-based architecture still requires network connectivity for resolution.

### 2. Ecosystem
Niche ecosystem within the Holochain community. Holochain has an interesting agent-centric architecture that differs from blockchain consensus models. Active development but limited mainstream adoption. Draft specification developed at RWOT9 (Rebooting Web of Trust) Prague 2019.

### 3. Stability
Prototype-level specification. Holochain itself is still evolving, which may affect long-term compatibility. The "draft-documents" location in the RWOT repository suggests the specification is not fully finalized.

## Recommendation
**No-go**

Requires Holochain infrastructure (DHT network, conductor, DeepKey) for DID resolution. Node operators cannot verify DIDs without running Holochain components and connecting to the peer-to-peer network.
