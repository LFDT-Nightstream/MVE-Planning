# did:gns (GNU Name System)

| Property | Value |
|----------|-------|
| Specification | [LSD0005](https://lsd.gnunet.org/lsd0005/) |
| Organization | GNUnet / Fraunhofer AISEC / TU Munich |
| DID Format | `did:gns:<Base32GNS-encoded-public-zone-key>` |

## Overview
The did:gns method leverages the GNU Name System (GNS), a decentralized name system that is part of the GNUnet project. DIDs are based on public zone keys that are Base32GNS-encoded. Resolution uses GNS's distributed hash table architecture to look up DID_DOCUMENT resource records, providing censorship-resistant operation without relying on traditional DNS or blockchains.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based verification.** While GNS avoids traditional blockchain and DNS dependencies, it requires access to the GNS network infrastructure (distributed hash tables). Node operators would need to run GNS nodes or have access to the GNUnet DHT network. The resolution process involves querying external distributed infrastructure.

### 2. Ecosystem
Niche ecosystem within the GNUnet privacy/anonymity community. Academic backing from Fraunhofer AISEC and TU Munich lends credibility. However, adoption is limited compared to mainstream DID methods. The GNUnet project has a long history but remains relatively specialized.

### 3. Stability
Well-documented specification with academic rigor. The underlying GNS technology is mature within its domain. However, the DID method itself has limited real-world deployment and testing at scale.

## Recommendation
**No-go**

Requires access to GNUnet's distributed hash table network for resolution. Node operators cannot verify DIDs without external network queries to the GNS infrastructure.
