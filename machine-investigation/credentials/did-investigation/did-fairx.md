# did:fairx (FairX DID Method)

| Property | Value |
|----------|-------|
| Specification | [Link](https://github.com/fairxio/protocol/tree/main/did) (404 - Repository deleted) |
| Organization | FairX (Michael Dowling) |
| DID Format | `did:fairx:...` (Unknown - spec unavailable) |

## Overview

The did:fairx method was a DID method intended to work with the FairX platform, a cryptocurrency banking service built on the Stellar network. FairX was founded by Michael Dowling, former CTO of IBM Blockchain Financial Solutions.

According to the W3C DID Spec Registries entry:
- **Verifiable Data Registry**: FairX Node
- **Status**: Deprecated
- **Contact**: Michael Dowling (mdowling@gmail.com)

FairX shut down in July 2019 due to inability to secure funding. The company's stated goal was to create a "new, licensed, fully regulated national bank, modeled as a financial market utility" that would facilitate crypto-to-fiat conversion on the Stellar network.

The specification repository (github.com/fairxio/protocol) no longer exists. The fairxio GitHub organization has zero public repositories, and all previous code repositories appear to have been deleted or made private.

## Evaluation

### 1. Feasibility/Complexity

**Cannot be assessed - specification unavailable**

Without access to the specification, we cannot determine:
- The DID identifier format
- How DIDs are created or resolved
- What cryptographic methods are used
- Whether resolution requires access to FairX Node infrastructure

Given that the verifiable data registry was listed as "FairX Node," resolution would likely have required connecting to FairX infrastructure that no longer exists.

### 2. Ecosystem

**Non-existent**

- **Company status**: FairX shut down in July 2019
- **GitHub presence**: Zero public repositories
- **Libraries/tools**: None available
- **Community**: None
- **Universal Resolver support**: None

The only remaining trace of this DID method is:
- The W3C DID Spec Registries entry (marked deprecated)
- A cached Go package reference for ISO-20022 financial messaging (unrelated to DID)

### 3. Stability

**Abandoned / Deprecated**

- **Last known activity**: July 2019 (company shutdown)
- **Specification**: Deleted/unavailable
- **W3C Registry status**: Officially deprecated
- **Maintainer**: Michael Dowling (company no longer exists)

## Special Considerations

- **Historical context**: FairX was an ambitious project attempting to bridge traditional banking with cryptocurrency using Stellar. The DID method was likely intended to provide identity infrastructure for this banking service.
- **No recoverable documentation**: Despite extensive searching including Wayback Machine attempts, no archived version of the specification could be found.
- **Stellar connection**: The method was likely tied to Stellar network, but technical details are unknown.

## Recommendation

**No-go**

The did:fairx method cannot be supported for the following reasons:

1. **Specification unavailable**: The specification repository has been deleted, making implementation impossible
2. **Deprecated status**: Officially marked as deprecated in the W3C DID Spec Registries
3. **No infrastructure**: FairX shut down in 2019; any required resolver infrastructure no longer exists
4. **Zero ecosystem**: No libraries, tools, or community support
5. **No users**: With the company shut down, there are no users of this DID method

Even if the specification were available, the method would likely require access to "FairX Node" infrastructure that no longer exists, making it fundamentally incompatible with our requirement that nodes be able to verify DIDs without external HTTP calls or blockchain access.
