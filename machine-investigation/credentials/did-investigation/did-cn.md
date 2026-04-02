# did:cn (China Industrial Internet DID Method)

| Property | Value |
|----------|-------|
| Specification | [CN DID Method Spec](https://github.com/caict-4iot-dev/CN-DID-Method-Specification) |
| Organization | CAICT-4IoT-Dev |
| DID Format | `did:cn:<issuer>.<industry>.<enterprise>` |

## Overview

The did:cn method integrates traditional Chinese industrial identifiers into the W3C DID architecture, enabling verifiable and self-managed digital identities for Chinese industrial entities.

Example DID: `did:cn:88.100.1`

### DID Structure

- **Issuer**: 2 digits (issuing authority)
- **Industry**: 3-5 digits (industry classification)
- **Enterprise**: 1-128 digits (entity identifier)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Apply for identifier, generate keypairs, submit to national registry |
| Read | Query national blockchain registry, verify signatures |
| Update | Sign update request with control keys |
| Deactivate | Sign deactivation request, permanent status change |

### Supported Algorithms

- Ed25519 (Ed25519Signature2020)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:cn method requires:

- **National blockchain registry**: Centralized Chinese government-controlled registry
- **Application process**: Entities must apply for identifiers
- **Industry classification**: Tied to Chinese industrial standards
- **Authority-issued**: Identifiers issued by authorized authorities

**Adaptation possibility**: None. This is a government-controlled identity system for Chinese industry, not a general-purpose DID method.

### 2. Ecosystem

**Regional / Government-controlled**

- **Chinese government**: CAICT (China Academy of Information and Communications Technology)
- **Industrial focus**: Designed for industrial internet entities
- **Regulatory compliance**: Tied to Chinese regulations
- **Limited documentation**: Minimal English documentation

### 3. Stability

**Government-backed**

- **Version**: 1.0.0
- **Institutional backing**: Chinese government agency
- **Minimal activity**: Few commits, no releases
- **Regional scope**: China-specific

## Special Considerations

- **Not self-sovereign**: Identifiers are issued by authorities
- **Industry-specific**: Tied to Chinese industrial classification
- **Hierarchical structure**: Issuer → Industry → Enterprise
- **Privacy**: Recommends off-chain storage for sensitive data

## Recommendation

**No-go**

The did:cn method is unsuitable for our use case:

1. **Government-controlled**: Requires Chinese government registry access
2. **Regional limitation**: Only applicable to Chinese industrial entities
3. **Not self-sovereign**: Identifiers issued by authorities, not self-created
4. **Application required**: Cannot freely create identifiers

This is fundamentally a different model from self-sovereign identity - it's a government-issued identity framework using DID syntax.
