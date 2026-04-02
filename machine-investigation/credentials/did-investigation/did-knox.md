# did:knox (Knox Networks)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/knox-networks/knox-did-specification) |
| Organization | Knox Networks |
| DID Format | `did:knox:<base58_identifier>` |

## Overview
Knox DID is a W3C-conforming decentralized identifier method designed for institutional contexts such as bank consortiums. Notably, it explicitly avoids blockchain dependency, instead using distributed database technologies for higher scale and reliability. The method uses Ed25519Signature2020 as the default cryptographic system.

## Evaluation

### 1. Feasibility/Complexity
**Requires trusted operator infrastructure but not blockchain.** The method:
- Explicitly avoids blockchain to prevent "expensive consensus algorithms"
- Uses distributed database technologies for the verifiable data registry
- Requires a "trusted operator" (e.g., bank consortium) to host the registry
- Supports full CRUD operations including revocation

Resolution occurs through Resolver functions that retrieve complete DID Documents from the registry. The trusted operator model means this is not fully decentralized but also not blockchain-dependent.

### 2. Ecosystem
The GitHub repository is archived (September 2025), suggesting the project may be discontinued or absorbed into other Knox Networks products. The institutional focus (banks, consortiums) indicates enterprise-oriented design.

### 3. Stability
The specification follows W3C standards with support for multiple verification methods (authentication, capability invocation/delegation, assertion). The archived status raises concerns about ongoing maintenance and support.

## Recommendation
**No-go**

While blockchain-independent, the method requires trusted operator infrastructure (distributed database registry). The archived repository status suggests the project may be abandoned. Cannot function in a self-contained manner.
