# did:andorra (Andorra National Registry DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:andorra Method Spec](https://github.com/davidgbvargroup/did-andorra-method-spec/blob/main/spec.md) |
| Organization | Govern d'Andorra / VarGroup |
| DID Format | `did:andorra:NRTAD-<6digits><letter>[_ISS\|_SP]` |

## Overview

The did:andorra method is a government-backed DID system tied to Andorra's National Registry of Legal Entities (NRTAD). DIDs are automatically generated for entities registered in the NRTAD and are managed exclusively by Andorra's official PKI infrastructure.

Example DIDs:
- `did:andorra:NRTAD-710646J`
- `did:andorra:NRTAD-059888N_ISS` (Issuer role)
- `did:andorra:NRTAD-059888N_SP` (Service Provider role)

### DID Structure

- **Prefix**: `did:andorra:`
- **Identifier**: `NRTAD-` + 6 digits + 1 uppercase letter
- **Optional suffix**: `_ISS` or `_SP` for role specification
- **Regex**: `^did:andorra:NRTAD-[0-9]{6}[A-Z](?:_(ISS|SP))?$`

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Not supported by external parties - tied to NRTAD registration |
| Read | `GET https://definitiveid.wsg127.com/definitiveid_services/rest/Public/Identity/{did}` |
| Update | Not supported by external parties - managed by Andorra PKI |
| Deactivate | Not supported by external parties - managed by Andorra PKI |

### Supported Algorithms

- JsonWebKey2020 verification method type
- Keys bound to NRTAD-issued digital certificates
- Follows eIDAS QTSP (Qualified Trust Service Provider) framework

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:andorra method is a read-only, government-controlled system:

- **Read-only for external parties**: Only resolve operations are available; create/update/deactivate are not supported
- **Government-controlled**: DIDs are tied to Andorra's national registry (NRTAD)
- **Centralized resolution**: Requires querying a specific government API endpoint
- **PKI dependency**: Keys managed exclusively by Andorra's official PKI through QTSPs

**Adaptation possibility**: None. This is fundamentally a government identity system, not a general-purpose DID method. The DIDs represent legal entities registered with Andorra's government, and all key management is handled by official PKI infrastructure.

### 2. Ecosystem

**Government-specific / Very limited**

- **National scope**: Only for entities registered in Andorra's NRTAD
- **Single jurisdiction**: Limited to Andorra
- **New specification**: Version 0.1.2, recently registered
- **No Universal Resolver driver yet**: Planned but not implemented
- **eIDAS aligned**: Follows EU electronic identification framework

### 3. Stability

**Early stage but government-backed**

- **Version 0.1.2**: Early specification
- **Semantic versioning**: Promises prior notice for breaking changes
- **Government backing**: Stability tied to Andorra government systems
- **eIDAS framework**: Built on established EU regulatory framework

## Special Considerations

- **Legal entity identity**: Designed for legal persons (companies), not individuals
- **Certificate-based**: Keys come from official digital certificates
- **Automatic revocation**: Revoked/expired certificates immediately reflected in DID resolution
- **Role suffixes**: Supports ISS (Issuer) and SP (Service Provider) role designation

## Recommendation

**No-go**

The did:andorra method is unsuitable for our use case:

1. **Read-only**: External parties cannot create DIDs - they must be registered legal entities in Andorra
2. **Government-controlled**: All operations managed by Andorra's national registry and PKI
3. **Centralized resolution**: Requires querying government API - cannot verify on-ledger
4. **Jurisdictional scope**: Only applicable to Andorra-registered legal entities

This is essentially a wrapper around Andorra's national identity system for legal entities, not a general-purpose DID method. It has legitimate use cases for Andorra's digital government services but is not applicable to our registry.
