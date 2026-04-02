# did:corda (Corda DID Method)

| Property | Value |
|----------|-------|
| Specification | [Corda DID Method](https://github.com/persistentsystems/corda-did-method) |
| Organization | Persistent Systems |
| DID Format | Unknown (specification HTML not accessible) |

## Overview

The did:corda method is designed for the Corda distributed ledger platform, an enterprise blockchain focused on business transactions. The specification is maintained as an HTML file in a GitHub repository.

Note: The specification HTML file could not be directly accessed for detailed analysis.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

Based on available information:

- **Corda dependency**: Requires Corda distributed ledger network
- **Enterprise DLT**: Corda is a permissioned enterprise platform
- **Private network**: Corda networks are typically private/consortium

**Adaptation possibility**: Very low. Corda is a fundamentally different architecture - a permissioned DLT for enterprise use cases, not a public blockchain.

### 2. Ecosystem

**Enterprise / Corda-specific**

- **Persistent Systems**: Enterprise software company
- **Corda ecosystem**: Limited to Corda network participants
- **Enterprise focus**: Financial services and enterprise use cases
- **R3 backing**: Corda maintained by R3 consortium

### 3. Stability

**Enterprise stable**

- **Corda mature**: Corda is a mature enterprise platform
- **Enterprise backing**: R3 and enterprise partners
- **Permissioned model**: Controlled deployment environments

## Special Considerations

- **Permissioned ledger**: Not a public blockchain
- **Enterprise focus**: Financial institutions, supply chain
- **Privacy features**: Corda emphasizes transaction privacy
- **Different model**: Notary-based consensus, not mining

## Recommendation

**No-go**

The did:corda method is unsuitable for our use case:

1. **Corda dependency**: Requires access to Corda network
2. **Enterprise/permissioned**: Not a public network
3. **Different architecture**: Notary-based consensus model
4. **Access requirements**: Typically requires consortium membership

Corda's architecture is fundamentally different from what we're building, making this method inapplicable.
