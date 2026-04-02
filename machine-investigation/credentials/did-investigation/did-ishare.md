# did:ishare (iSHARE Framework)

| Property | Value |
|----------|-------|
| Specification | [iSHARE DID](https://did.ishare.eu/) |
| Organization | iSHARE Foundation |
| DID Format | `did:ishare:<continent>.<country>.<organizationIdentifier>` |

## Overview
iSHARE DID is a method designed for legal entity identification in data-sharing ecosystems. DIDs are derived from credentials and digital certificates issued by recognized authorities within the iSHARE Framework. The method leverages existing PKI infrastructure (eIDAS eSEALs, x509 certificates) rather than blockchain technology.

## Evaluation

### 1. Feasibility/Complexity
**Requires external infrastructure (non-blockchain).** The method depends on:
- iSHARE Framework Participant Registry for validation
- Certificate Authorities (eIDAS eSEALs and similar frameworks)
- Certified Identity Providers for credential sources
- Authority-issued digital certificates or verifiable credentials

The DID document is minimal (essentially just the ID), with validation occurring against external certificate infrastructure. Resolution involves decoding the ID and verifying against valid certificates.

### 2. Ecosystem
Specialized ecosystem for European data-sharing initiatives. iSHARE has adoption in logistics and supply chain sectors, particularly in the Netherlands and broader EU.

### 3. Stability
The specification is maintained by iSHARE Foundation with formal governance through the Constituent Advisory Board (CAB). The reliance on established PKI standards (eIDAS) provides regulatory stability.

## Recommendation
**No-go**

Requires iSHARE Framework infrastructure, PKI certificate authorities, and participant registry for validation. Cannot function independently without these external trust frameworks.
