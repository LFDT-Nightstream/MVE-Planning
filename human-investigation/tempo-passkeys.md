# Tempo Passkeys Investigation

## Overview

[Tempo](https://tempo.xyz) is a blockchain that implements native passkey-based authentication at the protocol level. Their onboarding UX eliminates browser extensions, mobile apps, and seed phrases entirely — users sign up with a passkey (Face ID, Touch ID, YubiKey, etc.) and get a self-custodial wallet instantly.

This investigation focuses on what Midnight would need to support in order to reuse Tempo's libraries and achieve a similar passkey-powered UX.

## Midnight Compatibility Requirements

To reuse Tempo's ecosystem of libraries, Midnight would need two major additions:

### 1. ETH-style Address Format

Tempo uses standard EVM-compatible **0x addresses** (20 bytes). Addresses are derived from P-256 public keys:

```
address = last_20_bytes(keccak256(abi.encode(pubkey.x, pubkey.y)))
```

Where `x` and `y` are the 32-byte coordinates of the P-256 public key.

Midnight would need to either:
- Support this address format natively (alongside any existing format), or
- Provide a mapping/translation layer

### 2. P-256 (secp256r1) Curve Support

WebAuthn passkeys produce **P-256 (secp256r1)** signatures — this is hardcoded into the WebAuthn spec because P-256 is natively supported by all hardware secure enclaves (Apple Secure Enclave, Android Keystore, YubiKeys). This is a different curve than the secp256k1 used by Bitcoin/Ethereum traditionally.

Tempo supports three signature types at the protocol level:

| Key Type | ID | Curve | Use Case |
|---|---|---|---|
| secp256k1 ECDSA | 0 | secp256k1 | Traditional EVM wallets |
| P-256 ECDSA | 1 | secp256r1 | Secure enclave / WebCrypto keys |
| WebAuthn | 2 | secp256r1 | Passkeys (with origin binding + user presence) |

Midnight would need P-256 signature verification in its consensus/validation layer.

## P-256 vs secp256k1

| Aspect | P-256 (secp256r1) | secp256k1 |
|---|---|---|
| Origin | NIST standard | Koblitz curve (Bitcoin/Ethereum) |
| Hardware support | Native on all modern devices | Not natively supported |
| Standards | WebCrypto, TLS, FIDO2, PKI | Bitcoin, Ethereum |
| Security level | ~128 bits | ~128 bits |
| Performance | Slower (more field operations) | Faster (special structure) |
| WebAuthn compatible | Yes | No |

The fundamental incompatibility: hardware-backed passkeys produce P-256 signatures, but traditional blockchains verify secp256k1. This is the core gap Tempo solves by supporting both natively.

## Tempo Account Model

### Keychain Architecture

A Tempo Account is a **keychain** of authorized keys, managed by the Account Keychain precompile at `0xAAAAAAAA00000000000000000000000000000000`:

- **Root Key** (`keyId = address(0)`): The passkey (P-256/WebAuthn) that created the account. This is the user's biometric-backed key (Face ID, Touch ID, YubiKey). Full authority — can authorize new keys, revoke keys, update spending limits.
- **Access Keys** (aka session keys): Secondary keys authorized by the Root Key. Support multiple signature types (secp256k1, P-256, WebAuthn). Each can have:
  - Expiry timestamps
  - Per-token spending limits
  - Scoped permissions

This is analogous to SSH `authorized_keys` or OAuth refresh tokens across devices.

### Cross-App / Cross-Device Usage

Tempo uses **cross-origin iframe embedding** for SSO-like wallet experience:
- Third-party apps embed a thin iframe from `wallet.tempo.xyz`
- The iframe has `allow="publickey-credentials-create; publickey-credentials-get"` permission policy
- Passkey ceremony runs inside the iframe, bound to the `tempo.xyz` RP ID
- Communication via `postMessage`

Security hardening:
- **Access Keys**: The embedded experience authorizes a scoped Access Key for the third-party app (not the root signer)
- **IntersectionObserverV2**: Detects clickjacking attempts — if iframe is obscured, pops out to standalone window

### Tempo Transactions (EIP-2718 type 0x76)

Custom transaction format with:
- Batched calls (atomic multi-call execution)
- **2D nonces** (nonce_key + nonce) — enables parallel transaction streams from a single account
- `valid_before` / `valid_after` timestamps
- Fee token preference (pay fees in any stablecoin, no native token required)
- Fee payer signature (sponsored transactions, magic byte 0x78)
- Key authorization field for delegated permissions

### Passkey Recovery

If you lose access to your passkey:
- Passkeys sync via OS keychain (iCloud Keychain, Google Password Manager) or password managers (1Password)
- Access keys on other devices can still sign transactions
- Can add Ledger, YubiKey, or other keystores as backup/2FA

## Package Ecosystem Map

### Chain-Agnostic (reusable regardless of chain)

| Package | npm | Description | Stack Layer |
|---|---|---|---|
| **webauthx** | `webauthx` | Minimal WebAuthn toolkit for passkey registration & authentication. 3 functions for the auth ceremony across server and client. Sets conventional defaults for broad browser/authenticator compatibility. | Client + Server |
| **Viem** | `viem` | TypeScript Ethereum client library. Abstractions over JSON-RPC. First-class smart contract APIs, ABI encoding/decoding, wallet integration. | Core client library |
| **Wagmi** | `@wagmi/core`, `@wagmi/react`, `@wagmi/vue` | Reactive primitives for Ethereum apps. Framework-agnostic with React/Vue/Solid bindings. Porto is available as a wagmi connector. | App framework |
| **ox** | `ox` | Low-level, stateless Ethereum TypeScript primitives. Standard library for ABI, address, ECDSA, hex, RLP, transactions. Includes `WebAuthnP256` module (successor to archived `webauthn-p256`). | Foundational utilities |
| **ABIType** | `abitype` | Strict TypeScript types for Ethereum ABIs. Type-safe ABI handling without codegen. | Dev tooling / types |

### Tempo/Ithaca-Specific

| Package | npm | Description | Stack Layer |
|---|---|---|---|
| **Porto SDK** | `porto` | TypeScript SDK for creating, managing, and interacting with passkey-powered accounts. Integrates with Wagmi as a connector. Also supports direct Viem usage via `Porto.create()`. Works with wallet libraries (Privy, ConnectKit, RainbowKit, Dynamic, etc.). | Client SDK |
| **Porto Account Contracts** | `@ithaca/account` | EIP-7702 smart contract implementation. WebAuthn/passkey auth, batch transactions, gas sponsorship, access control, session keys, multi-sig. | On-chain (Solidity) |
| **Relay** | (infrastructure) | Cross-chain transaction routing for EIP-7702 accounts. Requires RIP-7212 (secp256r1 precompile) and `eth_simulateV1`. | Infrastructure |
| **reth-p256** | (Rust crate) | P-256 elliptic curve support for the Reth Ethereum client. Implements the RIP-7212 precompile. | Node infrastructure |

### Related / Reference

| Package | Source | Description | Stack Layer |
|---|---|---|---|
| **webauthn-p256** | `webauthn-p256` (archived) | P-256 WebAuthn utilities. Migrated into ox library's `WebAuthnP256` module. | Client (deprecated) |
| **Daimo p256-verifier** | `github.com/daimo-eth/p256-verifier` | Only audited open-source Solidity P-256 signature verifier. Deterministic CREATE2 deployment across EVM chains. | On-chain reference |
| **p256 (Rust)** | `p256` crate | Pure Rust P-256 implementation with ECDSA + ECDH support. Part of RustCrypto. | Rust crypto library |

## What Midnight Needs

For Midnight to adopt passkey-based accounts similar to Tempo:

### Protocol-Level

- [ ] **P-256 signature verification** in Nightstream / consensus rules — this is what passkeys (the root key) produce
- [ ] **Address derivation** that accepts P-256 public keys (ETH-style or a Midnight equivalent) — the account address is derived from the passkey's P-256 public key
- [ ] **Multiple signature algorithm support** in transaction validation (at minimum P-256 for the passkey root key; optionally also secp256k1 and WebAuthn for access keys)
- [ ] **Account abstraction** — keychain model where the passkey is the root key, and access keys (session keys) can be authorized for scoped, time-limited use by dApps and other devices

### Client-Side

- [ ] **WebAuthn integration** — use `webauthx` or similar for passkey registration (creating the root key) and authentication (signing with it)
- [ ] **Wallet SDK** — either adapt Porto SDK or build equivalent that targets Midnight instead of Ethereum RPCs. This is what manages the passkey root key and issues access keys (session keys) to dApps.
- [ ] **Wagmi/Viem adapter** — if reusing the wevm stack, need a Midnight chain definition + RPC transport

### Considerations

- **Chain-agnostic packages** (webauthx, ox's WebAuthnP256) can likely be reused as-is
- **Tempo-specific packages** (Porto SDK, Porto Account Contracts, Relay) would need adaptation or reimplementation for Midnight's transaction format and RPC layer
- The Rust `p256` crate from RustCrypto could be used directly in Midnight's Rust-based node implementation
- **Gas/cost model**: P-256 verification is ~100x more expensive than secp256k1 in EVM without a precompile; Midnight would want native support rather than doing this in a circuit

## PRF Extension (Future Opportunity)

The WebAuthn **PRF (Pseudo-Random Function) extension** allows each passkey to derive unique encryption keys per site/application. The authenticator evaluates a PRF using a device-stored secret key + relying party input, producing a deterministic 32-byte value.

Use cases for Midnight:
- **Per-dApp session key derivation** — hardware-backed, no seed phrase
- **End-to-end encrypted private state** — derive encryption keys for dApp-specific data
- **Account recovery** — deterministic key derivation from passkey

Platform support (as of early 2026): Android (Google Password Manager), macOS (Safari 18+, Chrome 132+), iOS 18.4+.

Moxie Marlinspike (Signal founder) is using PRF in his new project Confer for E2E encrypted chat, which validates the approach.

## Known Limitations of Passkeys

1. **WKWebView on mobile** — passkeys don't work in iOS WKWebView (used by apps like X.com for in-app browsing). Workaround: guide user to open in Safari or use `ASWebAuthenticationSession`.
2. **Total device + cloud loss** — if you lose all devices AND aren't logged into iCloud/1Password AND didn't add access keys, funds are unrecoverable. Same as losing a seed phrase with no backup.
3. **Browser support gaps** — Android phones pre-2018, devices without Google Play Services, legacy desktop OS (Windows 7/8). Overall coverage: 95% phones, 97% browsers.

## Sources

- [Tempo Docs: Use Accounts](https://docs.tempo.xyz/guide/use-accounts)
- [Tempo Docs: WebAuthn & P256 Signatures](https://docs.tempo.xyz/guide/use-accounts/webauthn-p256-signatures)
- [Tempo Docs: Tempo Transaction Spec](https://docs.tempo.xyz/protocol/transactions/spec-tempo-transaction)
- [Tempo Architecture Analysis: Account Abstraction](https://medium.com/@organmo/tempo-architecture-analysis-1-tempos-account-abstraction-6babdeabc93e)
- [EIP-7212: Precompiled for secp256r1 Curve Support](https://ethereum-magicians.org/t/eip-7212-precompiled-for-secp256r1-curve-support/14789)
- [webauthx (GitHub)](https://github.com/wevm/webauthx)
- [Porto (GitHub)](https://github.com/ithacaxyz/porto)
- [Porto Docs](https://porto.sh)
- [Daimo p256-verifier (GitHub)](https://github.com/daimo-eth/p256-verifier)
- [p256 Rust crate](https://docs.rs/p256)
- [RustCrypto/elliptic-curves](https://github.com/RustCrypto/elliptic-curves)
- [PRF WebAuthn (Bitwarden)](https://bitwarden.com/blog/prf-webauthn-and-its-role-in-passkeys/)
- [FIDO2/WebAuthn Architecture (Duo)](https://duo.com/blog/webauthn-passwordless-fido2-explained-componens-passwordless-architecture)
