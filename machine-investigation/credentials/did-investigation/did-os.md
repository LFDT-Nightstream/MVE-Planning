# did:os (OpenSig DID Method)

| Property | Value |
|----------|-------|
| Specification | [did-os-method-spec.md](https://github.com/OpenSig/opensig-protocol/blob/main/did/did-os-method-spec.md) |
| Organization | OpenSig (info@opensig.net) |
| DID Format | `did:os:[<chain-id>:]<b58-encoded-address>` |

## Overview

The did:os method is designed for the OpenSig protocol, providing decentralized identifiers anchored to Ethereum-compatible blockchain addresses. DIDs are derived from a controller's EOA (Externally Owned Account) or smart account address that publishes signatures to the OpenSig registry.

The method uses Base58-encoded Ethereum addresses as the unique identifier portion. The chain-id is optional and defaults to Polygon (137) if omitted. The address must decode to a valid 20-byte Ethereum-compatible address.

Example DIDs:
- `did:os:137:2m7abdAX7eUfrrxhSRRtMPz54bFL` (explicit Polygon chain ID)
- `did:os:2m7abdAX7eUfrrxhSRRtMPz54bFL` (implicit Polygon default)

DID documents use the `EcdsaSecp256k1RecoveryMethod2020` verification method and include expiration timestamps to prevent stale permissions.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

Resolution requires active blockchain interaction:
1. For EOAs: Resolution is derived from the decoded Base58 address, which could theoretically be done offline
2. For smart accounts: Resolution requires calling the ERC-1271 `isValidSignature(hash, sig)` method on-chain to verify signer authorization

The specification explicitly requires RPC endpoints to supported blockchain networks (Ethereum, Polygon) to verify smart account signatures. EOA-only verification might be possible by recovering the signer from an ECDSA signature, but the full method as specified requires blockchain calls.

Additionally, DIDs cannot be updated (new ones must be created), and DID documents have built-in expiration, which could complicate offline validation scenarios.

### 2. Ecosystem

**Early stage ecosystem.**

- Reference implementation available at https://github.com/opensig/opensig-ts
- Limited to secp256k1 keys only
- Supports Ethereum mainnet (chain ID 1) and Polygon (chain ID 137)
- No evidence of widespread adoption or third-party tooling
- OpenSig appears to be a relatively new project

### 3. Stability

**Immature specification.**

- No version number or last updated date in the specification
- Specification appears to be in active development
- Limited documentation beyond the method spec
- Contact only via email (info@opensig.net)
- Single organization maintaining the project

## Special Considerations

- **No update mechanism**: DIDs cannot be updated; users must create new DIDs instead
- **Expiring DID documents**: Documents include expiration timestamps to prevent stale permissions
- **Pseudonymity support**: Users can remain pseudonymous unless they voluntarily de-anonymize
- **Smart account support**: Supports both EOAs and ERC-1271 compatible smart accounts (e.g., account abstraction wallets)
- **Limited deactivation**: No direct deactivation method; smart accounts deactivate by removing authorized keys from the account

## Recommendation

**No-go**

The did:os method requires blockchain RPC calls for full resolution, particularly for smart account signature verification via ERC-1271. This creates a hard dependency on external blockchain infrastructure that nodes cannot satisfy without making HTTP/RPC requests. While EOA signatures might theoretically be verifiable offline through ECDSA recovery, the method specification does not separate these cases, and the overall design assumes blockchain access.

Additionally, the ecosystem is very early stage with limited adoption, the specification lacks maturity indicators (version, dates), and the expiring DID document design adds complexity for offline validation scenarios. The inability to update DIDs and reliance on document expiration for security further complicates integration.
