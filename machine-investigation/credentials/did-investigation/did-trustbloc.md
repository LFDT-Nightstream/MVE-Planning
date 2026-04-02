# did:trustbloc (TrustBloc)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/trustbloc/trustbloc-did-method/blob/master/docs/spec/trustbloc-did-method.md) |
| Organization | SecureKey |
| DID Format | `did:trustbloc:<consortium-domain>:<document-id>` |

## Overview
did:trustbloc is a DID method built on Sidetree protocol over a permissioned ledger. Multiple independent stakeholders form consortiums to collectively manage shared DID registries. Each stakeholder operates their own Sidetree nodes on shared ledger infrastructure.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires extensive infrastructure:

- Discovery servers hosting configuration at `/.well-known/did-trustbloc/`
- Multiple stakeholder domains with signed configuration files
- Sidetree endpoints for DID operations
- Permissioned ledger infrastructure
- JWS-signed stakeholder configurations
- Well-Known DID Configuration for domain-DID linking

Resolution requires fetching consortium config, verifying stakeholder endorsements, and querying multiple Sidetree endpoints.

### 2. Ecosystem
**Medium.** SecureKey has enterprise focus with government projects. Active development. Well-architected multi-stakeholder design.

### 3. Stability
**Mature.** Sophisticated specification with clear governance model. Production deployments in enterprise/government contexts.

## Recommendation
**No-go**

Requires extensive infrastructure: discovery servers, stakeholder domains, Sidetree endpoints, and permissioned ledger. The multi-stakeholder consortium model adds complexity but doesn't enable self-contained operation.
