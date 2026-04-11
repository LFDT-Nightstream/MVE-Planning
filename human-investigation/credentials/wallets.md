# Credential support in various tools

## Common terms

Many historical proposals for identity leverage [Decentralized Identifiers](https://www.w3.org/TR/did-1.1/) (DIDs) as they allow "self-sovereign" identity (meaning users control their own identity) which is appealing in a blockchain context. However, there are many credentials that are not generated from DIDs - especially in controlled environments.

Typically the flow for these systems is that
1. An entity (using a key of some kind) issues a "verifiable certificate" (VC) to an identity (ex: university gives degree to student)
2. The identity can issue a "verifiable presentation" (VP) to an interested party (ex: prove they graduated by presenting their degree)

### Credential types

The ecosystem is converging around three formats for credentials:
- mDocs (Mobile Documents) for identity credentials. It expands the in-use [ISO 18013-5](https://www.iso.org/standard/91081.html) which defined mobile driver's license (mDL) credentials, with a new [ISO 23220](https://www.iso.org/standard/74910.html) to generalize to more types of identities. Notably, with the [ISO 13013-7](https://www.iso.org/standard/91154.html) extension to allow them to be used for online verification (machine <-> machine, instead of something like passport shown to human). mDocs are a general CBOR scheme that have a namespace field to define how the CBOR should be interpreted. For example, mDL (mDocs for driver's licenses) is an mDoc with a specific namespace being used which defines the fields typically needed in a driver's licenses. It is mostly adopted by governments (adoption elsewhere stems from the fact that it's easier to adapt to standards governments have picked than getting governments to change their standards). Unfortunately mDocs, mDL, and related specifications are all paywalled and copyrighted (but open source implementations are allowed). That includes the definitions of multiple namespaces, and how they work.
- [SD-JWT VC draft](https://datatracker.ietf.org/doc/draft-ietf-oauth-sd-jwt-vc/) is a [JWT](https://www.rfc-editor.org/rfc/rfc7519) based VC which takes SD-JWT (Selective disclosure for JSON Web Tokens) as defined in [RFC 9901](https://datatracker.ietf.org/doc/rfc9901/) for enabling selective disclosure. It has a lot of traction by leveraging the existing popular JWT ecosystem in authentication systems.
- VCDM (Verifiable Credentials Data Model) v2.0 as define by [W3C](https://www.w3.org/TR/vc-data-model-2.0/). Sometimes also called "W3C Verifiable Credential"s. It leverages [JSON-LD](https://json-ld.org/spec/) to try and build a significantly more flexible format:
    - JSON-LD allows linking in definitions of use-case specific standards (instead of depending on centralized registries of handling different use-cases like mDocs)
    - allows specifying which securing mechanism you use, ex: `application/vc+sd-jwt`
    - generally tries to be a superset of other standards, and thus supports SD-JWT VC → VCDM, as well as mDoc → VCDM. Inverse directly is sometimes supported

As far as DID compatibility:
- VCDM: built with DID compatibility in mind, and supports the flexibility in linking data needed to resolve chains of documents (often required for DID schemes)
- SD-JWT VC: supports specifying DIDs as fields, but defines no particular semantics to work with them (if you build a tool that supports SD-JWT VCs with DIDs as fields, *you* as the developer need to handle resolving those DIDs and the logic that comes with that)
- mDocs: no "native" support for DIDs. You *can* have different mDoc namespaces that define DID concepts, but it's not inherit to the specification

### Protocols

These three credential formats are all compatible with OpenID initiatives to built protocols for handling these credentials as an extensions of OAuth 2.0
- [OpenID4VCI](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html) defines the protocol for triggering credential *issuance* (not the credentials themselves, which have to be encoded as one of the options like SD-JWT).
- [OpenID4VP](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html) defines the protocol for requesting *verifiable presentations* (doesn't define the presentation itself)

To facilitate storing and querying credentials, browsers have implemented have implemented
- a `navigator.credentials.*` module as part of the [Credential Management Level 1](https://www.w3.org/TR/credential-management-1/) which defines how passkeys are queried by the browser
- a [Digital Credentials API](https://www.w3.org/TR/digital-credentials/) (DC API) that extends the `navigator.credentials.*` module to also be able to store/query verifiable credentials
<!-- note: the Digital Credentials API replaces the older CHAPI API (which was not a true browser API, but meant to emulate what a native API would look like through standardized UI flow to motivate it a browser-native api like the DC API) -->

The Digital Credentials API ends up being the entry point for most applications, as different systems (OS, browser itself, 3rd party apps like 1password) can register themselves to listen for Digital Credentials API requests to handle them (ex: store your credentials in 1password, and have another site request them).  

Notably,
- the OpenID protocols can be sent over HTTPS as the transport, but also support sending over the Digital Credentials API instead.
- the mDoc ecosystem defines its own protocol (doesn't *have* to go through OpenID, and can be passed directly into the Digital Credentials API) 
- the Digital Credentials API could be extended to support more protocols in the future

Note that [DIDComm](https://identity.foundation/didcomm-messaging/spec/), a popular system for communication between DIDs that enables P2P communication (no https intermediate require)
    - If, in the Digital Credentials API, the applications is asking *directly* (through the browser API) to some system running on the user's device (either the OS itself, the browser, or some registered application like 1password) to provide certain credentials to it, why are concepts from OpenID (a protocol optimized for HTTPS communication) leveraged instead of a P2P protocol like DIDComm?
        - DIDComm enables long-running communication sessions, which the Digital Credentials API does not need
        - DIDComm requires both parties in the communication to have DIDs, but in the Digital Credentials API the requesting party (the app asking for the user's credential) usually doesn't have a DID
        - DIDComm is DID-centric, but the Digital Credentials API aims to support any time of Verifiable Credential (not just ones related to DIDs)
        - DIDComm requests are meant to be encrypted (to avoid man-in-the-middle), but the Digital Credentials API needs requests to potentially be visible by multiple parts of the stack (the browser, the OS, identity management apps) to know who can/will handle this request.

### Cryptography

Different cryptography is used to achieve Anonymous Credentials (AC)

Note: these credentials have support for different ways to achieve common cryptographic goals:
1. Selective disclosure (ex: prove >18 without revealing anything else about the ID)
2. Unlinkable disclosure (presentations cannot be used to leak privacy cross-context)
    - Presentation Unlinkability: Same Verifier, multiple presentations (ex: can you tell if somebody's been to the same bar and proved >18 multiple times)
    - Verifier/Verifier Unlinkability: Two different Verifiers cannot determine that they received presentations from the same credential
    - Issuer/Verifier Unlinkability: Issuers cannot track where you use your ID
    - Pseudonyms: the ability for users opt-into having linkability
3. Equality proofs (ex: prove two different IDs have the same value for a "name" field, without revealing the name)

An easy example to see how this kind of property is possible, think of *blind signatures*:
1. The user prepares a credential request (ex: "age ≥ 18")
1. They "blind" this request by padding with random bytes (request || nonce)
1. The issuer (ex: university) signs the blinded data (do not know the request)
1. The user can "unblind" as they have the original <request, nonce>, and can use the signature as a valid signed credential

There have been a few efforts to providing these cryptographic properties:
- Google's [Longfellow ZK](https://github.com/google/longfellow-zk) which bring anonymous credentials to mDLs specifically (although aspires to cover more VC formats)
    - Supports unlinkability 
    - Implementation doesn't support equality proofs, but *should* be doable
- [SD-JWT](https://datatracker.ietf.org/doc/rfc9901/) which can be used both with SD-JWT VCs, as well as VCDM. It *limited* support for unlinkability
    - Presentation Unlinkability and Verifier/Verifier Unlinkability achieved by creating batches of credentials, like 10 copies of the same driver's license
    - Issuer/Verifier Unlinkability is not achievable
- [CL signature scheme](https://eprint.iacr.org/2001/019.pdf): old (2001), RSA based, never approved by NIST, and not supported by secure enclaves
    - Supports unlinkability
    - Supports equality proofs
- [PS signature scheme](https://eprint.iacr.org/2015/525): newer iteration on CL signatures that, with some extra constraints and assumptions, achieve better signature size
    - Supports unlinkability
    - Supports equality proofs
- [BBS+](https://datatracker.ietf.org/doc/draft-irtf-cfrg-bbs-signatures/) is an elliptic-curve ([BLS12-381](https://datatracker.ietf.org/doc/draft-irtf-cfrg-pairing-friendly-curves/)) scheme that is [standardized for use in VCs](https://www.w3.org/TR/vc-di-bbs/). Note: it's based on a series of papers (not just one)
    - It supports unlinkability through a Schnorr-style proof of knowledge, and not through some complex ZK scheme
    - It doesn't support equality proofs (not due to a fundamental restriction on the cryptography, but just because the standard). Although it supports aggregating VPs, it only allows disclosing combined <name, value> pairs (cannot prove a "name" field exists, with same value in two different places, without revealing what the "name" is (ex: "Alice"))

Note: [AnonCred](https://www.lfdecentralizedtrust.org/projects/anoncreds) is not on the list, as AnonCred is a combination of things, not a specific scheme
- AnonCred v1 used CL signatures (combined with its own custom credential format)
- [AnonCred v2](https://github.com/anoncreds/anoncreds-v2-rs) is still in (slow, very occasional) development, and exact scheme used is still being iterated on, along with which credential format to use

Note: for presentations, sometimes full unlinkability is *too much* privacy, as it allows for sybil attacks (ex: sign up to the same website many times by pretending to be many different people 18+). To tackle this, some schemes (ex: BBS+) allow defining "Pseudonym"s. However, these *require extra support from the issuer* (for the same reason [SyRA](../SyRA.md) also requires extra work from the issuer for sybil-resistant zkLogin). However, may of these schemes require cryptography we don't (yet) easily have in the lattice setting (ex: specific kinds of VRFs in SyRA)

Note: none of these standards are post-quantum. Deciding on a post-quantum scheme is one of the ongoing [work streams](../../methodology/streams.md)

#### Revocations

Some systems require revocation in case credentials are lost / compromised.

Usually this is handled by having the credential owner manage an "accumulator" of revoked IDs, and VPs not need to be verified with two steps:
1. Verify claim itself (ex: over 18)
2. Proof credential is not in the revocation list

The technique used is usually a bitstring managed by the issuer that allocates one bit to all issued credentials, with the value representing the revocation status. At one bit per credential, this easily scales to large user sets (1 MB is 8m bits uncompressed. In practice, since most entries are 0, the compression factor is ver big). This means the list can easily be downloaded in its entirety by the user (to avoid leaking which entry belongs to a user).

There are two standards for this:
- [Token Status List (TSL)](https://datatracker.ietf.org/doc/draft-ietf-oauth-status-list/) for mDocs and SD-JWT VCs
- [Bitstring Status List v1.0](https://www.w3.org/TR/vc-bitstring-status-list/) for VCDM / JSON-LD

#### Usage of cryptography

While there are many cryptographic schemes (many having seen *some* usage in production), the industry currently primarily leverages
- Longfellow
- SD-JWT
- BBS+

## EU

Europe has historically been one of the leaders in pushing for DID adoption, and have few different efforts related to the tech

### EUDI

[EUDI](https://eudi.dev/latest/) Wallet (EU Digital Identity Wallet) aimed for 2026 brings digital identity.

For example, every EU member state must have one Digital Identity Wallet (EUDI) wallet by the end of 2026.

The scope of EUDI is quite broad (anything that supports relevant regulation like [eIDAS](https://en.wikipedia.org/wiki/EIDAS)("electronic IDentification, Authentication and trust Services")). That means that, although country-specific apps are more focused on locked-down systems where countries deploy country-specific apps that supports a pre-specific country-specific list of ID systems, the multi-nation nature of it means the specifications are quite flexible which enables any company in general to build EUDI-compatible wallets that support any issuer.

The technical backing of EUDI is based on the [ARF - Architecture and Reference Framework](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/4bc646e23f725464c09477d16d8fc9bdc2c8439a/docs/architecture-and-reference-framework-main.md). 

EUDI breaks down credentials into three categories with different strictness:
1. Electronic Attestation of Attributes from Public Bodies (PuB-EAA). These cover driver's licenses, Health Insurance, etc.
1. Qualified Electronic Attestation of Attributes (QEAA). These cover government-approved entities like universities, licensed professions, etc.
1. Non-qualified EAA. These cover private companies that are not explicitly approved.

The ARF specification locks down bureaucratic processes for every step of the pipeline for both PuB-EAA and QEAA, as documents they hold (by law) have the same legal effect as attestations in paper form. Notably, it controls
- Who can issue credentials
- Who can run EUDI wallets
- Who can verify a user's credentials (declaring which attributes they'll request and why)

That means that, in practice
1. Wallets that want to handle QEAA/PuB-EAA have to be approved (follow bureaucratic processes), but *can* also support Non-qualified EAAs.
1. Wallets that are just for Non-Qualified EEAs don't have to register themselves, but won't be able to handle PuB-EAA and QEAA credentials.

For compatibility with standards:
- The EUID allows implementers to support the Digital Credentials API, and is likely to move fully to it as it gets standardized.
- The EUID mandates mDocs and SD-JWT VC, and optionally allows VCDM (and for non-qualified EAAs only).

eIDAS specifically requires support for:
- pseudonyms
- selective disclosure
- unlinkability

#### Summary

Finding an EUID wallet that supports Non-Qualified EEAs that fit our requirements is a possibility. Unlike some other credential wallets, EUID does not differentiate tiers of credentials with different functionality. Rather, Non-Qualified EAAs have access to the same underlying technology (ex: selective disclosure) that is available to publicly issues credentials.

### EBSI / ESSIF

[European Blockchain Services Infrastructure](https://ec.europa.eu/digital-building-blocks/sites/spaces/EBSI/pages/447687044/Home) (EBSI) is a more DID-focused approach by the EU leveraging Hyperledger Besu in a federated mode to store DIDs and credential updates (through `did:ebsi`).

However, the project is in maintenance mode (not outright deprecated, but efforts have shifted to EUID over DIDs). Its parent project ([European Self-Sovereign Identity Framework](https://essif-lab.eu/) (ESSIF)) has explicitly ended.

## Japan

Japan, although previously a strong proponent of DID, seems to have backed off and is not monitoring the EUID project as potential inspiration for its own initiatives, as well as has integrated some of its government-issues IDs into the Apple Wallet ecosystem.

## Apple

Unlike previous solutions like [ID Verifier](https://developer.apple.com/wallet/id-verifier/) which was optimized for device -> human proof of documents (ex: concert tickets), Apple has rolled out a suite of APIs to allow software ↔ software document verification systems.

Apple has added ID support in Apple Wallet in two ways

### Passkit

[PassKit](https://developer.apple.com/documentation/passkit) supports use-cases like reward programs. These can only be issued by those with an Apple Developer Account. These are used to create "Wallet Passes" stored in the user's wallet (note: PassKit more broadly includes other systems like Apple Pay as well)

These are for simpler use-cases only:
1. Have limited software ↔ software interactions (Apple system, not based on standards)
1. Assume the existence of a centralized server managing pass data
1. Are not secured by the Secure Enclave

### ID in Wallet

[ID In Wallet](https://learn.wallet.apple/id) support a more open and cryptographic standard for identities;

1. Build upon the open [W3C Digital Credentials API](https://www.w3.org/TR/digital-credentials/) standard and [mDocs](https://www.iso.org/standard/91081.html)
1. Still optimized for centralized identity systems (ex: government issued IDs) and not Self-Sovereign Identities (SSI)
1. Secure by the Secure Enclave (cannot be duplicated across devices)
1. Supports selective disclosure

These IDs come in two flavors:
1. Built-in [Digital ID](https://www.apple.com/newsroom/2025/11/apple-introduces-digital-id-a-new-way-to-create-and-present-an-id-in-apple-wallet/) system closed for private government partners, optimized for selective disclosure, including projects like for US passports in Apple Wallet.
2. Extensible [Identity Document Services](https://developer.apple.com/documentation/IdentityDocumentServices) system to allow any app to register handles for identity requests with the [Document Provider API](https://developer.apple.com/documentation/IdentityDocumentServices/Implenting-as-an-identity-document-provider) (part of Identity Document Services). The app can chose its own UI for presenting the permission prompt for accessing the ID via the Digital Credentials API. The app can be specific to the ID, but there are also many general identity wallets that exist.

*Note*: you cannot mint your own ID and store in Apple Wallet (only specific Digital IDs can do this). Other than the closed partner list, IDs have to be stored in 3rd party apps (which can surface themselves as able to handle the credential type through the Identity Document Services). This means that the [Verify With Wallet API](https://developer.apple.com/wallet/get-started-with-verify-with-wallet/) (which connects to Apple Wallet) is not usable with these credentials (but both can be targeted by the Digital Credentials API).

### Summary

The functionality we need to have identities in Apple Wallet is not publicly available. We can still support Apple devices through a native-feeling flow, but it would require users to download a 3rd party identity wallet instead of leveraging the built-in Apple Wallet.
