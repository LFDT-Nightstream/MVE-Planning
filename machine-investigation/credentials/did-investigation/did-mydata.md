# did:mydata (Data Agreement DID Method)

| Property | Value |
|----------|-------|
| Specification | [Link](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/did-spec.md) |
| Organization | iGrant.io (Sweden) |
| DID Format | `did:mydata[:did-type-integer]:mb-value` |

## Overview

The did:mydata method is designed to support the Automated Data Agreement (ADA) framework, enabling GDPR-compliant personal data transactions. It uses Ed25519 key pairs encoded with Multibase/Multicodec standards.

**DID Structure:**
- `did-type-integer` (optional): Identifies the entity type (0=Data Source, 1=Data Subject, 2=Data Using Service, 3=Assessor, 4=Auditor)
- `mb-value`: Multibase base58-btc encoded Ed25519 public key

Example: `did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E`

**Resolution:** DIDs are resolved via DIDComm messaging protocol. A `read-did` message is sent to an ADA service endpoint (discovered via `/.well-known/did-configuration.json`), which responds with the DID document. All messages use JWE encryption (anoncrypt packing).

**CRUD Operations:**
- Create: Generate Ed25519 keypair, send DIDComm `create-did` message to ADA service
- Read: Send DIDComm `read-did` message to resolve DID document
- Update: **Not permitted** in current specification
- Delete: Send DIDComm `delete-did` message to mark DID as revoked

## Evaluation

### 1. Feasibility/Complexity

**No-go for our use case.** The did:mydata method fundamentally requires HTTP communication with centralized ADA service endpoints:

- DID resolution requires sending DIDComm messages to an ADA service via HTTP POST
- Service discovery depends on fetching `/.well-known/did-configuration.json` from HTTP endpoints
- There is no way to verify or resolve a did:mydata identifier without network access to the issuing ADA service
- The DID document is not self-contained; the public key in the DID identifier alone is insufficient for verification without resolving the full document from the registry

The architecture is centralized around ADA service providers rather than being self-certifying or ledger-independent.

### 2. Ecosystem

**Limited adoption:**
- Single organization (iGrant.io) maintains the specification and implementation
- No independent SDKs or libraries found outside of iGrant.io's ecosystem
- GitHub repository: 13 stars, 6 forks (as of research date)
- Part of EU-funded NGI-eSSIF-Lab project, which has limited broader adoption
- Registered in W3C DID Method Registry but without significant third-party implementations

### 3. Stability

**Immature and unmaintained:**
- Specification version 1.1, marked as "Work in progress"
- Last specification update: August 2021 (over 4 years ago)
- Repository last pushed: February 2023
- Update operations explicitly not supported in current version
- No active development or specification evolution observed

## Special Considerations

- **Entity Type System:** The DID format includes an optional type integer that classifies the DID holder's role (Data Source, Data Subject, Data Using Service, Assessor, Auditor). This is unique but adds complexity.
- **GDPR Focus:** Designed specifically for GDPR compliance and data agreement workflows, making it a niche solution rather than a general-purpose DID method.
- **Immutable Documents:** DID documents cannot be updated once created, only revoked. This is a significant limitation for many use cases.
- **DIDComm Dependency:** All operations require DIDComm protocol support, adding implementation complexity.

## Recommendation

**No-go**

The did:mydata method cannot be supported in our system due to its fundamental architecture:

1. **Resolution requires HTTP requests:** There is no way to verify a did:mydata identifier without making HTTP calls to an ADA service endpoint. The DID identifier contains only an Ed25519 public key, but verification requires fetching the full DID document from a centralized registry.

2. **Centralized dependency:** The method relies entirely on ADA service providers as centralized registries. Without access to these services, DIDs cannot be resolved or verified.

3. **No self-certifying properties:** Unlike did:key or did:peer, the did:mydata format does not encode enough information to independently verify signatures or derive verification keys without external resolution.

4. **Limited ecosystem and maintenance:** The specification has been stagnant since 2021 with minimal adoption outside of iGrant.io's own projects.

Given our constraint that nodes cannot make HTTP requests to validate DIDs, did:mydata is not viable for integration.
