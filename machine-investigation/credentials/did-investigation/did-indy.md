# did:indy (Hyperledger Indy)

| Property | Value |
|----------|-------|
| Specification | [Hyperledger](https://hyperledger.github.io/indy-did-method/) |
| Organization | Hyperledger Foundation |
| DID Format | `did:indy:<network>:<id>` |

## Overview
The did:indy method operates on Hyperledger Indy, a public ledger designed specifically for privacy-preserving self-sovereign identity. DIDs must be self-certifying (derived from initial public key used in NYM). The method supports subsidiary ledgers using ":" notation and KERI identifiers. NYM objects store DID data and are transformed into DID documents during resolution. Also supports referencing schemas, claim definitions, and revocation registries via DID URLs.

## Evaluation

### 1. Feasibility/Complexity
**Requires investigation for patterns.** Resolution requires access to a Hyperledger Indy ledger to read NYM transactions and transform them into DID documents. However, Indy's design is specifically optimized for verifiable credentials with interesting patterns:

- Self-certifying DIDs derived from public keys
- Schema, Claim Definition, and Revocation Registry as ledger objects
- Version_id and version_time resolution parameters
- Resource resolution via sequence numbers

The verifiable credential infrastructure (schemas, claim definitions, revocation registries) is well-designed for credential ecosystems.

### 2. Ecosystem
Large ecosystem as part of Hyperledger Foundation. Multiple production Indy networks (Sovrin, BCGov, etc.). Strong community with active development. Widely adopted for government and enterprise identity projects. Integration with AnonCreds for privacy-preserving credentials.

### 3. Stability
Mature specification with years of production use. Strong governance through Hyperledger Foundation. Multiple independent networks reduce single-point-of-failure concerns. Well-documented with community-driven development.

## Recommendation
**Requires investigation**

While direct resolution requires Indy ledger access (typically no-go), the method's design patterns for self-certifying DIDs and verifiable credential infrastructure are worth studying. The ecosystem maturity and AnonCreds integration offer lessons for privacy-preserving credential systems.
