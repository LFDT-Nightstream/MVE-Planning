# DID support in various tools

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
