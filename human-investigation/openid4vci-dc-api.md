# OpenID4VCI + Digital Credentials API Investigation

## Overview

Can we represent an access key (derived from a passkey root key) as a **W3C Verifiable Credential**, issued via **OpenID4VCI** transported over the browser's **Digital Credentials API** (no HTTPS), and stored in an identity wallet for later verification or distribution to employees?

Short answer: **yes**, with one significant gotcha around WebAuthn signing. There are two realistic ways around it, each with different tradeoffs.

## The Three Moving Parts

### 1. W3C Verifiable Credentials Data Model (VCDM 2.0)

A Verifiable Credential is a JSON/JSON-LD document with an issuer, subject, claims, and a cryptographic proof. The model is extensible — you can define custom `@context` vocabularies and `type` values for your own credential shapes.

For a derived key credential, the shape would look like:

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://midnightos.example/key-credential/v1"
  ],
  "type": ["VerifiableCredential", "DerivedKeyCredential"],
  "issuer": "did:key:<root-public-key>",
  "validFrom": "2026-04-09T00:00:00Z",
  "validUntil": "2026-04-16T00:00:00Z",
  "credentialSubject": {
    "id": "did:key:<access-public-key>",
    "publicKeyJwk": { "kty": "EC", "crv": "P-256", "x": "...", "y": "..." },
    "parentKey": "did:key:<root-public-key>",
    "capabilities": ["sign"],
    "spendingLimit": "1000"
  },
  "proof": {
    /* signed by the root key */
  }
}
```

The security vocabulary already includes `capabilityDelegation`, `capabilityChain`, and `parentCapability` terms that align with root→child key hierarchies. The proof binds the credential to the root key as a verifiable attestation of delegation.

### 2. OpenID for Verifiable Credential Issuance (OpenID4VCI)

Standard protocol for an issuer to deliver a VC to a wallet. Normal flow:

1. Wallet → Authorization Server (authorization code or pre-authorized code)
2. Wallet → Token Endpoint (exchange code for access token)
3. Wallet → Nonce Endpoint (get a fresh nonce for proof-of-possession)
4. Wallet → Credential Endpoint (send credential request with PoP, receive VC)

Supports multiple VC formats: `jwt_vc_json`, `jwt_vc_json-ld`, `ldp_vc` (all W3C VCDM) plus SD-JWT and mDL.

### 3. Digital Credentials API (DC API)

Browser API (in development at W3C / WICG) that lets a web page request credentials from the user's device wallet without HTTPS round-trips to an issuer. The API is **protocol-agnostic**:

```js
navigator.credentials.get({
  digital: {
    providers: [{ protocol: "openid4vci", request: <credential-request-body> }]
  }
});
```

The DC API doesn't care about the semantics of the protocol body — it just passes it opaquely to the wallet handler registered on the device. This means you can use OpenID4VCI message formats **without HTTPS or an external issuer**.

## The Flow for Access Key Credentials

Putting it together:

1. User has a root key (passkey in secure enclave) and has generated an access key (software P-256)
2. Some dApp (or the wallet UI itself) wants a Verifiable Credential for the access key
3. The dApp calls DC API with an OpenID4VCI credential request as the body
4. A local handler on the device responds: builds a VC envelope, signs with the root key, returns the VC
5. The VC is stored in the wallet and can be presented to verifiers later

## The WebAuthn Signing Gotcha

Here's where it gets tricky. The proof on a standard W3C VC is typically one of:

- **JWT-VC**: the whole VC is a JWT signed by the issuer
- **Data Integrity Proof** (formerly Linked Data Proof): a proof object with a signature over a canonicalized hash of the VC

Both require the issuer to produce an **arbitrary signature over a hash**. But WebAuthn passkeys can't do that. WebAuthn signatures are always over:

```
authenticatorData || sha256(clientDataJSON)
```

Where the thing you actually care about signing has to be embedded inside `clientDataJSON.challenge`. The signature carries all this WebAuthn envelope baggage.

Sui hit this exact wall when adding passkey support — they created a dedicated signature scheme (flag `0x06`) specifically to carry the WebAuthn envelope for verification. Standard W3C VC verifiers won't know how to unwrap this.

## Two Options

### Option A: Custom WebAuthn Proof Type

Define a cryptosuite like `webauthn-p256-2026` and a proof type like `Es256WebAuthn` that carries `{authenticatorData, clientDataJSON, signature}` in the proof object:

```json
"proof": {
  "type": "Es256WebAuthn",
  "cryptosuite": "webauthn-p256-2026",
  "verificationMethod": "did:key:<root>#keys-1",
  "proofValue": "<ecdsa-signature-hex>",
  "webauthn": {
    "authenticatorData": "base64url...",
    "clientDataJSON": "base64url..."
  }
}
```

A verifier reconstructs `authenticatorData || sha256(clientDataJSON)`, verifies the signature with the root public key, then checks that `clientDataJSON.challenge` matches the canonicalized VC hash.

**Tradeoffs:**

- Pro: single VC, root key is the direct issuer
- Pro: clean delegation model — no indirection through a second key
- Pro: same pragmatic move Sui made with their `0x06` signature scheme
- Con: non-portable — existing W3C VC verifiers (1Password, Apple Wallet, generic SSI tooling) can't verify it without support for the custom cryptosuite
- Con: needs a cryptosuite spec we define and maintain (unless/until one gets standardized upstream)

### Option B: Indirection via Access Key

Don't have the root key sign the VC directly. Instead:

1. The root key (passkey) signs a **key authorization** — WebAuthn signature over a challenge containing the access key's public key. This is exactly what our demo already produces via `/api/authorize-key`.
2. The access key (software) signs the VC using a standard proof type (JWT-VC or Data Integrity with `EcdsaP256`).
3. The VC includes the key authorization as a `capabilityChain` or embeds a second VC attesting the delegation.

The verifier checks two things:

- The VC's direct proof (access key signed it) using standard verifier logic
- The capability chain: the root key authorized the access key (by verifying the WebAuthn envelope once, outside the VC proof path)

**Tradeoffs:**

- Pro: the VC itself uses standard proof types → portable, works in existing wallets
- Pro: maps directly to what we already built in the demo
- Con: verifiers must still understand the capability chain (a W3C concept, but less widely implemented than basic VC verification)
- Con: two-step verification, indirect issuer semantics

## Mapping to Our Existing Demo

Our current passkey demo already has most of the pieces for either option:

- `/api/register/*` — creates the passkey (root key)
- Access key generated client-side via Web Crypto
- `/api/authorize-key/*` — passkey signs a challenge containing the access key pubkey, server stores the authorization

For **Option A**, the `/api/authorize-key` endpoint essentially becomes an issuer endpoint. Instead of storing a bare authorization record, it builds a full VC envelope with the access key in `credentialSubject`, sets the challenge to the canonicalized VC hash, and wraps the WebAuthn response as a `webauthn-p256-2026` proof. The access key never signs anything for the VC — the passkey is the direct issuer.

For **Option B**, the existing authorization record is reused as-is (carried in the VC as `capabilityChain`), and the access key gains a new responsibility: signing the VC it's embedded in using a standard proof type.

Either way, no new crypto — it's a formatting and transport layer on top of what already exists.

## OpenID4VCI Over DC API: What Collapses

Standard OpenID4VCI expects multiple round-trips (authorization, token, nonce, credential). Over DC API you get a single request-response.

The fit: **pre-authorized code flow**. This OpenID4VCI variant skips the authorization endpoint entirely. The credential request can be sent directly with a pre-auth code (or no code at all, since the local handler is implicitly authorized by the user approving the DC API prompt).

Realistic subset of OpenID4VCI we'd actually use:

- Section 8 (Credential Request/Response) — the core message format
- Section 7 (Nonce) — optional; DC API's own challenge mechanism may substitute
- NOT Sections 5-6 (Authorization/Token endpoints) — skipped via pre-auth flow
- Key Attestation (Appendix D) — natural fit, since the secure enclave can attest the passkey's hardware binding

## What Midnight Would Need

Shared work regardless of option:

- **Access key credential vocabulary** — define a `DerivedKeyCredential` `@context` with `parentKey`, `capabilities`, `spendingLimit`, etc.
- **Local DC API handler** — a device-side component that accepts OpenID4VCI credential requests and returns a signed VC. In a browser-only world this is a Service Worker or a wallet extension. For midnightOS, a native device component.

Option A additional work:

- **`webauthn-p256-2026` cryptosuite spec** — document canonicalization rules, how the challenge is derived from the VC hash, and how `authenticatorData || sha256(clientDataJSON)` is verified against `proofValue`
- **Verifier support for the custom cryptosuite** — for Nightstream, a circuit that verifies the WebAuthn envelope and extracts the access key from `credentialSubject`

Option B additional work:

- **`capabilityChain` handling in the verifier** — parse the authorization record, verify the WebAuthn envelope for the root-to-access-key delegation, then verify the VC's own standard proof

## Honest Summary

| Aspect                                                 | Feasibility                                        |
| ------------------------------------------------------ | -------------------------------------------------- |
| Representing access keys as W3C VCs                    | Fully supported via extensibility                  |
| OpenID4VCI message format                              | Works for the credential request/response portion  |
| Transporting via DC API (no HTTPS)                     | Supported — DC API is protocol-agnostic            |
| Secure enclave as "issuer" (self-issuance)             | Unconventional but mechanically possible           |
| Full multi-step OpenID4VCI over DC API                 | Requires pre-authorized code flow, single-exchange |
| Root key signing VC directly via WebAuthn envelope     | Works with a custom proof type (Option A)          |
| Root key delegating to access key, access key signs VC | Works with standard proof types (Option B)         |
| Storing result in identity wallets                     | Standard — it's a valid W3C VC                     |

## Sources

- [OpenID for Verifiable Credential Issuance 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)
- [W3C Digital Credentials API](https://www.w3.org/TR/digital-credentials/)
- [W3C Verifiable Credentials Data Model v2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- [Tempo Passkeys Investigation](./tempo-passkeys.md) — what we already built
- [Sui Passkeys Investigation](./sui-passkeys.md) — the WebAuthn envelope problem in practice
