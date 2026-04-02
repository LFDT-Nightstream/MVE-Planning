# did:grg (GrgChain)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/GrgChain/DID-method-specs/blob/master/README.md) |
| Organization | GrgChain |
| DID Format | `did:grg:<ethereum-address>` |

## Overview
The did:grg method is developed by GrgChain for use within alliance blockchain ecosystems. DIDs are based on Ethereum-compatible addresses derived from hierarchical deterministic wallet paths. The method uses Chinese cryptographic algorithms (SM2 and SM4) alongside standard cryptography, with a focus on real-name authentication and credential verification.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Resolution explicitly requires connection to an external HTTP resolver service (REST API endpoint). The SDK documentation specifies a ResolverURL configuration parameter pointing to a server that handles DID document retrieval. Additionally, the system relies on an alliance blockchain for storing issuer DID documents.

### 2. Ecosystem
Limited ecosystem primarily targeting Chinese enterprise and alliance blockchain use cases. The use of Chinese national cryptographic standards (SM2/SM4) may limit international adoption. Documentation is available but the project appears to have limited community engagement.

### 3. Stability
Early-stage specification tied to a specific regional blockchain initiative. The reliance on proprietary resolver infrastructure raises questions about long-term availability and decentralization.

## Recommendation
**No-go**

Requires external HTTP resolver service for DID resolution. Node operators cannot verify DIDs without making external network requests to the GrgChain resolver API.
