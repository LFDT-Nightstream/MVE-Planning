# did:aergo (Aergo DID Method)

| Property | Value |
|----------|-------|
| Specification | [Aergo DID Method Spec](https://github.com/aergoio/aergo-identity/blob/master/doc/did-method-spec.md) |
| Organization | Aergo |
| DID Format | `did:aergo:[mainnet\|testnet:]<aergo-address>` |

## Overview

The Aergo DID method allows any Aergo smart contract or key pair account to become a valid decentralized identity. The design is similar to did:ethr - identities require no explicit registration and are controlled by the holder of the corresponding private key.

Example DID: `did:aergo:AmgFiAupQBr7tx4CLkoV7uZhsMYDNjM5tsQUREKfdtwpGqsm3R9s`

### DID Structure

- **Prefix**: `did:aergo:`
- **Optional network**: `mainnet:` or `testnet:` (mainnet is default)
- **Aergo address**: 52 Base58Check characters

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | No registration required - any Aergo address is automatically a valid DID |
| Read | Query registry contract events and state to build DID document |
| Update | Call registry functions: `changeOwner`, `setAttribute`, `addDelegate` |
| Deactivate | Revoke attributes and delegates; transfer ownership to null address |

### Supported Algorithms

- **secp256k1** only (Secp256k1VerificationKey2018, Secp256k1SignatureAuthentication2018)
- Key encodings: Hex, Base64, Base58

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:aergo method is fundamentally tied to the Aergo blockchain:

- **Registry contracts required**: DID documents are built from on-chain registry contract state and events
- **Transaction funding**: All updates require funded Aergo accounts
- **Blockchain queries**: Resolution requires querying Aergo nodes for contract events (`DIDOwnerChanged`, `DIDDelegateChanged`, `DIDAttributeChanged`)

**Adaptation possibility**: Very low. This is essentially the same architecture as did:ethr but for the Aergo blockchain. We cannot verify Aergo blockchain state from our ledger without running Aergo nodes or implementing complex cross-chain verification.

Unlike some methods where we could swap the underlying ledger, the method is specifically designed around Aergo's contract model and event system.

### 2. Ecosystem

**Very limited**

- **Single blockchain**: Only works on Aergo mainnet/testnet
- **Minimal adoption**: Aergo is a relatively small blockchain platform
- **Limited tooling**: No widespread resolver support outside of Aergo's own implementations
- **No Universal Resolver driver**: Not listed in the DIF Universal Resolver drivers

### 3. Stability

**Uncertain**

- **No versioning**: Specification lacks version numbers or changelog
- **Single maintainer**: Appears to be maintained solely by Aergo team
- **Limited documentation**: Minimal examples and implementation guidance
- **Unclear activity**: Repository shows limited recent activity

## Special Considerations

- **Similar to did:ethr**: Nearly identical architecture to the Ethereum DID method, just for Aergo blockchain
- **Delegation support**: Supports key delegation with expiration timestamps
- **No registration required**: Like did:ethr, any address is implicitly a valid DID

## Recommendation

**No-go**

The did:aergo method has the same fundamental limitation as did:ethr - it requires access to a specific blockchain's state to resolve DIDs. From our ledger, we cannot trustlessly verify Aergo blockchain state without:
1. Running Aergo nodes
2. Implementing complex cross-chain state proofs

Additionally, the Aergo ecosystem is small with limited adoption, making this method unsuitable even if the technical barriers could be overcome. The specification provides no unique features that would justify the integration complexity over more widely adopted alternatives.
