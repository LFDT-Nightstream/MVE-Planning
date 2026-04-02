# did:lit (LEDGIS)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/ibct-dev/lit-DID/blob/main/docs/did:lit-method-spec_eng_v0.1.0.md) |
| Organization | IBCT (LEDGIS) |
| DID Format | `did:lit:<base58_21-22_chars>` |

## Overview
LEDGIS DID (did:lit) is a blockchain-based decentralized identifier method operating on the LEDGIS DID Chain. The method uses a system contract (`led.lit`) for all DID operations and implements a custom permission model with "controller permission" and "delegator permission" for access control.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work without external infrastructure.** The method requires:
- LEDGIS DID Chain infrastructure (`lit.ledgis.io`)
- System contract `led.lit` for all DID document management
- Blockchain tables (`did`, `controller`, `vcstatus`) for data persistence
- Custom blockchain permission model for access control

All operations are fundamentally tied to the LEDGIS blockchain infrastructure.

### 2. Ecosystem
Specialized ecosystem around the LEDGIS blockchain. The method supports comprehensive verification relationships (authentication, assertionMethod, keyAgreement, capabilityInvocation, capabilityDelegation) and uses EcdsaSecp256k1VerificationKey2019.

### 3. Stability
The specification (v0.1.0/v0.1.1) suggests early development stage. The custom permission model and blockchain-specific tables indicate a tightly coupled implementation that may be difficult to migrate or adapt.

## Recommendation
**No-go**

Requires LEDGIS blockchain infrastructure for all operations. Early stage specification with blockchain-specific implementation details.
