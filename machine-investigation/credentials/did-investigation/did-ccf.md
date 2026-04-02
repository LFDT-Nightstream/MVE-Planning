# did:ccf (Confidential Consortium Framework DID Method)

| Property | Value |
|----------|-------|
| Specification | [DID:CCF Spec](https://github.com/microsoft/did-ccf/blob/main/DID_CCF.md) |
| Organization | Microsoft |
| DID Format | `did:ccf:[domain|node_id]:[account_address]` |

## Overview

The did:ccf method operates within Microsoft's Confidential Consortium Framework (CCF), an open-source framework for building highly available stateful services using trusted execution environments (TEEs) and decentralized systems concepts.

Example DIDs:
- Domain-scoped: `did:ccf:example.com:Y0EI0lIbEm8nBvaWnogpg`
- Node-scoped: `did:ccf:entra.confidential-ledger.azure.com:EiClkZMDxPKqC9c`

### DID Structure

- **Domain**: 1-253 characters (alphanumeric, hyphens, dots)
- **Node ID**: 1-64 characters (alphanumeric, hyphens, dots)
- **Account Address**: 1-64 characters (alphanumeric, underscores, hyphens)

### CRUD Operations

| Operation | Endpoint | Access |
|-----------|----------|--------|
| Create | `/app/identifiers/create` | Private |
| Resolve | `/app/identifiers/<did>/resolve` | Public |
| Update | `/app/identifiers/<did>/keys/roll` | Private |
| Deactivate | `/app/identifiers/<did>/deactivate` | Private |

### Supported Algorithms

- RSASSA_PKCS1-v1_5
- ECDSA (secp256k1, secp256r1, secp384r1)
- EdDSA

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ccf method requires:

- **CCF Infrastructure**: Must run on a CCF network
- **Trusted Execution Environments**: Hardware-backed TEE (Intel SGX, AMD SEV) required
- **Consortium governance**: Multi-party proposal/voting mechanism
- **Domain registration**: Domains must be registered by consortium members

**Key insight**: Despite not being blockchain-based, CCF uses "centralized compute" with "decentralized trust" through hardware enclaves. This is fundamentally incompatible with our ledger-based approach.

**Adaptation possibility**: Very low. The entire security model depends on TEE hardware and CCF-specific governance structures.

### 2. Ecosystem

**Enterprise / Microsoft ecosystem**

- **Microsoft backing**: Strong corporate support
- **Azure integration**: Integrates with Azure Confidential Ledger
- **Working draft**: Still under development
- **Niche adoption**: Primarily enterprise use cases

### 3. Stability

**Active development**

- **Status**: Working Draft
- **Corporate backing**: Microsoft maintains actively
- **Implementation available**: API endpoints defined

## Special Considerations

- **Hardware requirements**: TEE (SGX/SEV) is not universally available
- **Confidentiality by default**: Good for enterprise privacy requirements
- **Consortium model**: Interesting governance pattern
- **Not blockchain**: Uses confidential ledger, not distributed ledger

## Recommendation

**No-go**

The did:ccf method is unsuitable for our use case:

1. **CCF dependency**: Requires running on CCF infrastructure with TEE hardware
2. **Governance mismatch**: Consortium model doesn't align with our ledger
3. **Azure-centric**: Primarily designed for Azure Confidential Ledger integration
4. **Hardware requirements**: TEE requirement limits deployment options

The governance patterns and key management approach are well-designed but the infrastructure requirements make adoption impractical.
