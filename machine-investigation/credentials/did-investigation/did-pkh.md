# did:pkh (Public Key Hash)

| Property | Value |
|----------|-------|
| Specification | [did-pkh-method-draft.md](https://github.com/w3c-ccg/did-pkh/blob/90b28ad3c18d63822a8aab3c752302aa64fc9382/did-pkh-method-draft.md) |
| Organization | W3C Credentials Community Group (W3C-CCG) |
| DID Format | `did:pkh:<caip2-chain-id>:<account-address>` |

## Overview

did:pkh is a generative "pseudo-DID method" similar to did:key that creates DID documents from blockchain addresses conformant with the CAIP-10 specification. It allows keypairs from any major blockchain to instantly generate a valid DID without requiring on-chain registration or external resolution.

The method derives DIDs from public key hashes (PKH), which are the standard identifier format used across most major blockchains. The DID document is algorithmically generated from the blockchain address itself, containing:
- A verification method with the `blockchainAccountId` property
- Authentication and assertion method references
- Network-specific verification method types (e.g., `EcdsaSecp256k1RecoveryMethod2020` for Ethereum, `Ed25519VerificationKey2018` for Solana)

**Supported Networks Include:**
- Bitcoin: `did:pkh:bip122:000000000019d6689c085ae165831e93:128Lkh3S7CkDTBZ8W7BbpsN3YYizJMp8p6`
- Ethereum: `did:pkh:eip155:1:0xb9c5714089478a327f09197987f16f9e5d936e8a`
- Solana: `did:pkh:solana:4sGjMW1sUnHzSxGspuhpqLDx6wiyjNtZ:CKg5d12Jhpej1JqtmxLJgaFqqeYjxgPqToJ4LBdvG9Ev`
- Tezos: `did:pkh:tezos:NetXdQprcVkpaWU:tz1TzrmTBSuiVHV2VfMnGRMYvTEPCP42oSM8`
- Polygon, Celo, Dogecoin, and others

## Evaluation

### 1. Feasibility/Complexity

**Favorable for offline verification.** Like did:key, did:pkh is a purely generative method where the DID document is derived algorithmically from the DID itself. No HTTP calls or blockchain access are required to resolve the DID document.

However, there are important considerations:
- **CAIP-2 chain ID parsing required**: The resolver must understand CAIP-2 chain identifiers to properly parse the DID
- **Network-specific address validation**: Each blockchain has its own address format (e.g., EIP-55 checksums for Ethereum, base58 for Bitcoin)
- **Multiple verification method types**: Different networks require different cryptographic verification methods to be included in the DID document
- **No key material in DID**: Unlike did:key which embeds the public key, did:pkh only contains the hash. Signature verification requires the full public key to be provided alongside signatures (recoverable from ECDSA signatures on Ethereum, but not universally)

**Complexity Assessment**: Medium. While the core concept is simple, supporting multiple blockchain address formats and verification method types adds implementation complexity.

### 2. Ecosystem

**Moderate adoption with focused use cases:**

- **Libraries**:
  - [pkh-did-resolver](https://www.npmjs.com/package/pkh-did-resolver) (npm, by 3Box Labs)
  - [SpruceID DIDKit](https://github.com/spruceid/didkit) with SSI library (Rust)
  - Universal Resolver driver available
- **Maintainers**: Wayne Chang, Charles Lehner, Juan Caballero, Joel Thorstensson
- **Integration**: Used in Sign-In with Ethereum (SIWE) and similar blockchain wallet authentication flows
- **Repository Stats**: 48 stars, 11 forks on GitHub

The primary use case is enabling blockchain wallet holders to participate in DID-based systems without creating a new identity - their existing blockchain address becomes their DID.

### 3. Stability

**Draft status with limited recent activity:**

- **Specification Status**: Draft
- **Last Repository Update**: February 2023 (last commit: 2023-02-15)
- **Last Push**: August 2023
- **Meeting Status**: Regular meetings are on "indefinite hiatus" since 2022
- **Open Issues**: 9

The specification has been relatively stable but development appears to have slowed. The core functionality is well-defined but the specification remains a draft without progression toward a final standard.

## Special Considerations

1. **No Key Rotation or Deactivation**: As a purely generative method, did:pkh does not support updates, key rotation, or deactivation. If a private key is compromised, the DID cannot be revoked. The spec explicitly warns against using did:pkh for "interactions that last weeks to months."

2. **Upgrade Paths**: did:pkh is designed as a "bridge" method. DIDs can be translated to blockchain-specific methods (did:tz, did:btcr, did:ethr) that support full DID document management when needed.

3. **Public Key Recovery**: For Ethereum (secp256k1), the public key can be recovered from signatures. For other chains like Solana (Ed25519), the public key must be provided separately since Ed25519 signatures do not support key recovery.

4. **Legacy Format Support**: The spec includes backward compatibility for older formats (e.g., `did:pkh:eth:` instead of `did:pkh:eip155:1:`).

5. **CAIP Dependencies**: Relies on CAIP-2 (chain identification) and CAIP-10 (account identification) specifications from Chain Agnostic Improvement Proposals.

## Recommendation

**Recommended** (with caveats)

did:pkh is well-suited for our constraint that nodes cannot make HTTP requests to validate DIDs. The DID document is entirely derivable from the DID string itself, similar to did:key.

**Reasons for recommendation:**
- Purely generative - no external resolution needed
- Well-defined specification with multiple implementations
- Enables interoperability with the large ecosystem of blockchain wallets
- Useful for short-lived, low-security authentication scenarios

**Caveats to consider:**
- Implementation complexity increases with each supported blockchain
- For practical signature verification, public keys must be provided alongside signatures (except for recoverable ECDSA on Ethereum)
- No support for key rotation means it should be limited to ephemeral or low-stakes use cases
- The specification is in draft status with development on hiatus

For initial implementation, consider supporting only the most common networks (Ethereum/EVM chains) and expanding based on demand.
