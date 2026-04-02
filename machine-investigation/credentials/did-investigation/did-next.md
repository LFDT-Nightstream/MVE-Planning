# did:next (Nextme DID)

| Property | Value |
|----------|-------|
| Specification | [Link](https://docs.nextme.one/#/DID/NextDID) |
| Organization | Next Labs |
| DID Format | `did:next:<method-specific-identifier>` |

## Overview

did:next is a DID method developed by Next Labs for the Nextme platform, a Web3 social gateway that creates decentralized identity profiles. The method is registered in the W3C DID Spec Registries and claims to have 142,000+ users with support for 1,900+ social media and publisher integrations.

The Nextme platform operates as a social dApp that allows users to create personalized profile pages (similar to Linktree but with Web3 capabilities). Users can log in via Web3 wallets (MetaMask, WalletConnect) or Web2 OAuth providers (Twitter, Discord, Instagram, etc.). The platform operates on Ethereum and Polygon blockchains.

**Note:** The actual technical specification for did:next is not publicly documented in detail. The specification link provided in the W3C registry leads to general documentation that lacks specific information about DID format, creation, resolution, and verification processes.

## Evaluation

### 1. Feasibility/Complexity

**Assessment: Not feasible without external dependencies**

The did:next method appears to rely on:
- **Nextme's proprietary "DIDs Network"**: A centralized infrastructure maintained by Next Labs
- **Blockchain access**: The platform operates on Ethereum and Polygon, requiring blockchain state access for verification
- **HTTP calls**: Resolution would likely require querying Nextme's servers or blockchain nodes

The lack of a detailed technical specification makes it impossible to determine exactly how DIDs are resolved. However, based on the architecture described (centralized platform with blockchain integration), nodes would almost certainly need to make HTTP requests to Nextme's infrastructure or access Ethereum/Polygon blockchain state to validate DIDs.

### 2. Ecosystem

**Assessment: Limited**

- **Adoption**: Claims 142,000+ users on the Nextme platform
- **Tools/Libraries**: No public SDKs or libraries found for DID resolution
- **Community**: Primarily focused on Nextme's social platform users rather than broader DID ecosystem
- **Integration**: No evidence of integration with standard DID tooling (Universal Resolver, etc.)

The ecosystem appears to be a closed system centered around the Nextme platform rather than a general-purpose DID method.

### 3. Stability

**Assessment: Immature/Unclear**

- **Specification**: The technical specification is not publicly documented in sufficient detail
- **Last Update**: DIDs Network launched in Q1 2023
- **Maintenance**: Active platform development, but unclear specification maintenance
- **W3C Registration**: Listed in W3C DID Spec Registries, but registration does not imply specification maturity

The lack of a proper specification document raises concerns about long-term stability and interoperability.

## Special Considerations

- **Centralized Infrastructure**: Despite being a "decentralized" identity method, the system appears heavily dependent on Nextme's centralized platform
- **Social Focus**: The method is designed specifically for social profile aggregation rather than general-purpose decentralized identity
- **Multi-chain**: Operates on both Ethereum and Polygon
- **Missing Specification**: The specification URL in the W3C registry does not contain detailed technical documentation about the DID format, CRUD operations, or resolution process
- **Freemium Model**: The platform operates on a freemium model, suggesting commercial rather than open infrastructure

## Recommendation

**No-go**

The did:next method cannot be supported for the following reasons:

1. **External Dependencies Required**: Resolution requires access to either Nextme's proprietary infrastructure or Ethereum/Polygon blockchain state, violating the constraint that nodes cannot make HTTP requests or access external blockchain state to validate DIDs.

2. **Insufficient Specification**: The lack of detailed technical documentation makes it impossible to implement a compliant resolver or understand the exact verification requirements.

3. **Centralized Architecture**: The method is tightly coupled to Nextme's platform infrastructure, making it unsuitable for truly decentralized verification.

4. **Limited Ecosystem**: No public libraries or tools exist for independent DID resolution outside the Nextme platform.

This method is fundamentally incompatible with a system that requires self-contained DID verification without external network calls.
