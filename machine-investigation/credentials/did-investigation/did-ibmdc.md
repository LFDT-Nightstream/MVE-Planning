# did:ibmdc (IBM Digital Credentials)

| Property | Value |
|----------|-------|
| Specification | [IBM Wiki](https://wiki.digitalcredentials.ibm.com/#/spec/v1/did-method) |
| Organization | IBM |
| DID Format | `did:ibmdc:<identifier>` |

## Overview
The did:ibmdc method is IBM's broader digital credentials DID method (distinct from did:hpass which is health-specific). It appears to be built on similar Hyperledger Fabric infrastructure as the Health Pass system. The specification is hosted on IBM's internal wiki (wiki.digitalcredentials.ibm.com), indicating enterprise-focused deployment.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for self-contained verification.** Based on available information and the relationship to IBM's other DID work, this method likely requires Hyperledger Fabric blockchain infrastructure. The specification being hosted on IBM's enterprise wiki rather than public GitHub suggests a proprietary or semi-proprietary approach.

### 2. Ecosystem
Enterprise ecosystem within IBM's digital credentials portfolio. Likely integrated with IBM Verify and other IBM identity products. Limited public documentation makes assessment difficult. The wiki-based specification hosting limits community visibility and contribution.

### 3. Stability
Backed by IBM's enterprise credibility. However, the closed nature of the specification (wiki access may be restricted) raises concerns about transparency and community governance. Long-term availability depends on IBM's product strategy.

## Recommendation
**No-go**

Requires IBM's proprietary infrastructure for DID resolution. Limited public specification access prevents thorough technical evaluation. Node operators cannot verify DIDs without access to IBM's credential systems.
