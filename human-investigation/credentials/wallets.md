# DID support in various tools

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

#### Conclusion

Finding an EUID wallet that supports Non-Qualified EEAs that fit our requirements is a possibility. Unlike some other credential wallets, EUID does not differentiate tiers of credentials with different functionality. Rather, Non-Qualified EAAs have access to the same underlying technology (ex: selective disclosure) that is available to publicly issues credentials.

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

1. Build upon the open [W3C Digital Credentials API](https://www.w3.org/TR/digital-credentials/) standard
1. Still optimized for centralized identity systems (ex: government issued IDs) and not Self-Sovereign Identities (SSI)
1. Secure by the Secure Enclave (cannot be duplicated across devices)
1. Supports selective disclosure

These IDs come in two flavors:
1. Built-in [Digital ID](https://www.apple.com/newsroom/2025/11/apple-introduces-digital-id-a-new-way-to-create-and-present-an-id-in-apple-wallet/) system closed for private government partners, optimized for selective disclosure, including projects like for US passports in Apple Wallet.
2. Extensible [Identity Document Services](https://developer.apple.com/documentation/IdentityDocumentServices) system to allow any app to register handles for identity requests with the [Document Provider API](https://developer.apple.com/documentation/IdentityDocumentServices/Implenting-as-an-identity-document-provider) (part of Identity Document Services). The app can chose its own UI for presenting the permission prompt for accessing the ID via the Digital Credentials API. The app can be specific to the ID, but there are also many general identity wallets that exist.

*Note*: you cannot mint your own ID and store in Apple Wallet (only specific Digital IDs can do this). Other than the closed partner list, IDs have to be stored in 3rd party apps (which can surface themselves as able to handle the credential type through the Identity Document Services). This means that the [Verify With Wallet API](https://developer.apple.com/wallet/get-started-with-verify-with-wallet/) (which connects to Apple Wallet) is not usable with these credentials (but both can be targetted by the Digital Credentials API).

### Summary

The functionality we need to have identities in Apple Wallet is not publicly available. We can still support Apple devices through a native-feeling flow, but it would require users to download a 3rd party identity wallet instead of leveraging the built-in Apple Wallet.
