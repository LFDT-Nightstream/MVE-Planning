# did:onion (Onion DID Method)

| Property | Value |
|----------|-------|
| Specification | [did-method-onion](https://blockchaincommons.github.io/did-method-onion/) |
| Organization | Blockchain Commons (Christopher Allen, Orie Steele) |
| DID Format | `did:onion:<tor-hidden-service-address>[:path]` |

## Overview
The did:onion method leverages Tor Hidden Services (onion services) as the foundation for decentralized identifiers. Instead of relying on a blockchain or distributed ledger, it uses Tor's existing infrastructure and security properties for identity anchoring and resolution.

A did:onion identifier contains a Tor v3 hidden service address (56 characters). Resolution involves constructing a URL from the DID (e.g., `did:onion:fscst5exmlmr262byztwz4kzhggjlzumvc2ndvgytzoucr2tkgxf7mid` resolves to `http://fscst5exmlmr262byztwz4kzhggjlzumvc2ndvgytzoucr2tkgxf7mid.onion/.well-known/did.json`) and fetching the DID document via HTTP over the Tor network.

The method supports optional paths for multi-user scenarios where a domain operator can delegate control of sub-paths to different users. DID documents use JsonWebKey2020 format with Ed25519 keys being the primary supported type.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for our use case.**

Resolution of did:onion requires:
1. Access to the Tor network
2. Making HTTP requests to onion services to fetch `did.json`

Nodes would need to route traffic through Tor to resolve any did:onion identifier. This fundamentally violates our constraint that nodes cannot make HTTP requests to validate DIDs. The DID document is not self-contained in the identifier - it must be fetched from the live hidden service.

Additionally, Tor onion addresses in v3 are derived from the service's public key, but the DID document itself (containing verification methods) is not cryptographically bound to the identifier - it's simply hosted at that address.

### 2. Ecosystem
**Limited ecosystem.**

- Reference implementations exist in JavaScript and Rust
- Maintained by Blockchain Commons, a reputable organization in the digital identity space
- However, adoption appears limited compared to methods like did:web or did:key
- The Tor network requirement creates a significant barrier for mainstream adoption

### 3. Stability
**Moderate stability.**

- The specification is well-documented and follows W3C DID conventions
- Maintained by Blockchain Commons with known editors (Christopher Allen, Orie Steele)
- Relies on Tor Hidden Services which have been stable for many years
- However, the spec explicitly notes a "lack of auditability in the read mechanism" as a security vulnerability

## Special Considerations
- **Privacy**: Tor provides strong anonymity guarantees, making this attractive for privacy-sensitive use cases
- **Censorship resistance**: Onion services are difficult to block or take down
- **No TLS required**: Tor already provides end-to-end encryption
- **Update mechanism**: DID document updates require modifying the hosted `did.json` file - there is no on-chain or cryptographically verifiable update history
- **Availability**: The hidden service must remain online for resolution to work

## Recommendation
**No-go**

The did:onion method is fundamentally incompatible with our constraint that nodes cannot make HTTP requests to validate DIDs. Resolution requires:
1. Tor network connectivity
2. Live HTTP requests to onion services

There is no way to verify a did:onion identifier without network access to the Tor hidden service. The DID document is not encoded in or derivable from the identifier itself - it must be fetched. This makes it unsuitable for offline or ledger-based verification scenarios where external network calls are not permitted.
