# did:nda (NDA Chain DID)

| Property | Value |
|----------|-------|
| Specification | [did:nda Method Specification](https://did-specs.ndachain.vn) |
| Organization | Vietnam's National Data Center |
| DID Format | `did:nda:0x[40 hex characters]` |

## Overview

The `did:nda` method is a hybrid DID architecture designed for secure, verifiable, and portable digital identities. It operates on the NDA Chain blockchain (an Ethereum-compatible network) with off-chain Verifiable Data Registry (VDR) storage. The method combines on-chain anchoring for integrity with off-chain document storage for privacy and scalability.

DIDs are derived from ECDSA secp256k1 key pairs, following Ethereum address generation: private key -> public key -> Keccak-256 hash -> truncation to 20 bytes -> hex encoding with `0x` prefix. This results in identifiers matching the pattern `^did:nda:0x[0-9a-fA-F]{40}$`.

The creation process has two phases:
1. **Off-chain generation**: Local key pair generation and DID derivation
2. **On-chain registration**: Publishing DID Document hash to NDA Chain for anchoring

Resolution occurs through the NDA DID Resolver service at `resolver.ndadid.vn`, which fetches DID Documents from off-chain VDR storage rather than querying the blockchain directly.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

The `did:nda` method has fundamental requirements that prevent local-only verification:
- **Blockchain dependency**: Verification requires accessing NDA Chain to confirm DID registration and document hash anchoring
- **Centralized resolver**: The specification mandates resolution through `resolver.ndadid.vn` to fetch DID Documents from off-chain VDR storage
- **No self-contained documents**: Unlike `did:key` or `did:peer`, the DID string alone does not contain enough information to derive the DID Document

Nodes would need to either:
1. Make HTTP calls to the NDA resolver service, or
2. Access NDA Chain blockchain state directly

Neither option is feasible under our constraints.

### 2. Ecosystem

**Limited ecosystem, regional focus.**

- Operated by Vietnam's National Data Center - government-backed but geographically limited
- No evidence of widespread adoption outside Vietnam
- No known open-source libraries or SDKs for implementation
- Provisional status in the W3C DID Method Registry
- Single resolver endpoint suggests limited infrastructure diversity

### 3. Stability

**Early stage, government-maintained.**

- Specification follows W3C DID Core v1.0 standards
- Semantic versioning policy for updates
- Government backing provides some stability assurance
- However, limited public development history and community involvement
- No visible public repository or changelog

## Special Considerations

- **Privacy-focused design**: Only DIDs and hashes stored on-chain; no PII on blockchain
- **Ethereum compatibility**: Uses standard secp256k1 keys and Ethereum-style addresses
- **Centralized trust model**: Security depends on NDA Chain integrity and resolver service availability
- **Vietnamese government context**: Designed for Vietnam's national digital identity infrastructure

## Recommendation

**No-go**

The `did:nda` method cannot be supported under our key constraint that nodes cannot make HTTP requests to validate DIDs or access external blockchain state. The method fundamentally requires:

1. Access to the NDA DID Resolver service (`resolver.ndadid.vn`) to fetch DID Documents from off-chain storage
2. Optionally, access to NDA Chain to verify document hash anchoring

The DID identifier itself (an Ethereum-style address) does not contain sufficient information to derive the DID Document locally. Additionally, the limited ecosystem focused on Vietnam's national infrastructure and the dependency on a single government-operated resolver make this unsuitable for our use case even if technical constraints were addressed.
