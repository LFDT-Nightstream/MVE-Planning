# did:safe (Gnosis Safe)

| Property | Value |
|----------|-------|
| Specification | [Ceramic CIP-101](https://cips.ceramic.network/CIPs/cip-101) |
| Organization | Safe Ecosystem Foundation |
| DID Format | `did:safe:eip155:<chainId>:<safe-address>` |

## Overview
did:safe converts Gnosis Safe smart contract instances into decentralized identifiers. It extracts Safe contract owners, maps them to DIDs via Ceramic's caip10-link, and constructs DID documents with those DIDs as controllers. Designed for multi-signature wallet identity.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires multiple external systems:

- **EVM blockchain nodes** to query Safe contract owners
- **Ceramic network** for caip10-link document lookups
- **The Graph** for historical block queries (versionTime resolution)
- CAIP-10 Account ID standard for cross-chain identification

Resolution involves three steps: extract Account ID, query Safe contract, lookup Ceramic links.

### 2. Ecosystem
**Medium.** Benefits from Gnosis Safe's significant adoption in the Ethereum ecosystem. Ceramic network integration adds complexity but enables cross-protocol identity.

### 3. Stability
**Moderate.** Documented as a Ceramic Improvement Proposal (CIP). Depends on stability of both Gnosis Safe and Ceramic protocols.

## Recommendation
**No-go**

Requires multiple external infrastructures: EVM blockchain, Ceramic network, and potentially The Graph. The multi-system dependency makes it unsuitable for self-contained operation.
