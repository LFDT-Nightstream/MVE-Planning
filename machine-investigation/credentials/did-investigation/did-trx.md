# did:trx (TRON)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/ontology-tech/DID-method-specs/blob/master/did-trx/DID-Method-trx.md) |
| Organization | Ontology Foundation |
| DID Format | `did:trx:<40-hex-chars>` |

## Overview
did:trx is a DID method for the TRON blockchain. The 40-character hexadecimal identifier represents a TRON address. Registration is implicit - anyone with a TRON address automatically owns the corresponding DID. Managed via smart contract registry.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires TRON blockchain:

- DID documents managed via registry smart contract
- Implicit creation (TRON address = DID ownership)
- Resolution via `get_document()` contract method
- Controller management via `add_controller()`, `remove_controller()`
- Deactivation via `deactivate_did()` is permanent and irreversible
- Uses EcdsaSecp256k1VerificationKey2019

### 2. Ecosystem
**Medium.** Benefits from TRON blockchain's significant user base. Developed by Ontology Foundation. Active blockchain ecosystem.

### 3. Stability
**Moderate.** Clear specification following W3C standards. Depends on TRON blockchain stability.

## Recommendation
**No-go**

Requires TRON blockchain for all DID operations. Cannot function without TRON network access.
