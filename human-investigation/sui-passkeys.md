# Sui Passkeys Investigation

## Overview

[Sui](https://sui.io) supports passkey-based transaction signing via a dedicated signature scheme (flag `0x06`). Like Tempo, it uses secp256r1/P-256 — the curve hardcoded in WebAuthn. The approach is fundamentally the same as what we already implemented in our demo: WebAuthn passkeys producing P-256 signatures.

## How It Works

Sui couldn't reuse their standard secp256r1 verification for passkeys because WebAuthn adds its own wrapping around what gets signed. Specifically:

- **Standard secp256r1 on Sui**: signs `blake2b_hash(intent || tx_data)` directly
- **Passkey on Sui**: WebAuthn signs `authenticatorData || sha256(clientDataJSON)` — the actual transaction data is embedded inside `clientDataJSON.challenge`

This forced a new signature scheme (`0x06`) with validators that understand the WebAuthn envelope.

## Verification (on-chain)

Validators do 6 steps:
1. Deserialize signature with flag `0x06`
2. Validate `clientDataJSON` has required WebAuthn fields (`type: "webauthn.get"`, `challenge`, `origin`, `crossOrigin`)
3. Verify the `challenge` field matches `intent || blake2b_hash(tx_data)`
4. Confirm sender address derives from the public key
5. Enforce secp256r1 flag in the inner signature
6. Run ECDSA verification over `authenticatorData || sha256(clientDataJSON)`

## Address Derivation

```
address = blake2b_hash(0x06 || compressed_p256_public_key)
```

## Notable Details

- **Public key only available at registration** — WebAuthn doesn't return it during signing, so wallets must cache it
- **Public key recovery** — if cache is lost, can recover from exactly 2 passkey signatures on different messages (ECDSA produces up to 4 candidates per signature, 2 signatures narrow to 1)
- **s-normalization** — signature's `s` value must be in the lower half of the curve order (standard malleability fix)
- **BCS serialization** — signature format: flag byte `0x02` + 64-byte r||s + 33-byte compressed public key

## Comparison to Tempo / Our Demo

| Aspect | Sui | Tempo | Our Demo |
|---|---|---|---|
| Curve | P-256 (secp256r1) | P-256 (secp256r1) | P-256 (secp256r1) |
| WebAuthn | Yes | Yes | Yes |
| Address derivation | blake2b(0x06 \|\| pk) | keccak256(x \|\| y) | did:key (multicodec + base58) |
| Signature scheme | Custom flag 0x06 wrapping WebAuthn | Native protocol support (key_type 2) | Standard ECDSA via Web Crypto |
| Access keys | No (single passkey per account) | Yes (root key + access keys) | Yes (passkey + software access key) |
| Cross-app via iframe | No | Yes (wallet.tempo.xyz embed) | Yes (passkeys.rvcas.dev/embed) |

## Key Takeaway

Nothing unique here relative to Tempo's approach. Both use P-256 WebAuthn passkeys. The main difference is Sui treats passkeys as a standalone signature scheme with no access key / keychain model — it's just "passkey = your only signer." Tempo's approach (root key + access keys + cross-app iframe) is strictly more capable.

For Midnight, the Sui approach confirms that any chain wanting passkey support needs P-256 signature verification in its consensus rules and a way to handle the WebAuthn envelope (authenticatorData + clientDataJSON wrapping).

## Sources

- [Sui Passkey Docs](https://docs.sui.io/concepts/cryptography/passkeys)
- [SIP-9 Specification](https://github.com/sui-foundation/sips/blob/main/sips/sip-9.md)
- [Mysten Labs TypeScript SDK - Passkey](https://sdk.mystenlabs.com/typescript/cryptography/passkey)
