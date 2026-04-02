# did:ethr (Ethereum DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:ethr Spec](https://github.com/decentralized-identity/ethr-did-resolver/blob/master/doc/did-method-spec.md) |
| Organization | DIF / uPort |
| DID Format | `did:ethr:[network:]<address-or-pubkey>` |

## Overview

The did:ethr method allows any Ethereum address or secp256k1 public key to become a valid DID without registration. It uses the ERC-1056 smart contract for optional key management, delegation, and attribute storage. This is one of the most widely deployed DID methods.

Example DIDs:
- `did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a`
- `did:ethr:mainnet:0xb9c5714089478a327f09197987f16f9e5d936e8a`
- `did:ethr:0x1:0xb9c5714089478a327f09197987f16f9e5d936e8a`

### DID Structure

- **Prefix**: `did:ethr:`
- **Network** (optional): `mainnet`, `goerli`, chain ID (e.g., `0x1`)
- **Identifier**: Hex-encoded Ethereum address (`0x...`) or compressed secp256k1 public key

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Free - any Ethereum address is automatically valid |
| Read | Query ERC-1056 contract events + state |
| Update | Contract calls for delegates, attributes |
| Deactivate | Transfer ownership to null address |

### Resolution Process

1. Call `changed(address)` to get latest modification block
2. Filter `DIDOwnerChanged`, `DIDDelegateChanged`, `DIDAttributeChanged` events
3. Follow `previousChange` pointers to build complete history
4. Construct DID Document from event data

### Key Rotation

Supported via delegation mechanism:
- `veriKey`: Added to verificationMethod + assertionMethod
- `sigAuth`: Added to verificationMethod + authentication
- Delegates have `validTo` timestamps for expiration

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:ethr method requires Ethereum blockchain access:

- **Smart contract queries**: Resolution requires calling ERC-1056 contract
- **Event log access**: Must read historical events from Ethereum
- **Multi-network**: Supports various EVM chains - increases complexity
- **State dependency**: Full features require on-chain state

**Generative aspect interesting:**
Like did:key, any Ethereum address is automatically a valid DID without registration. This "generative" pattern is valuable - but the advanced features (key rotation, services, delegates) all require blockchain state.

**Adaptation possibility**: Very low. We cannot verify Ethereum state from our ledger without:
1. Running Ethereum nodes
2. Implementing cross-chain proofs
3. Trusting external APIs (centralization)

### 2. Ecosystem

**Excellent / Industry standard**

- **DIF specification**: Decentralized Identity Foundation
- **Wide adoption**: One of the most used DID methods
- **Universal Resolver**: Full support
- **uPort/Veramo**: Major implementations
- **Multi-chain**: Works on many EVM chains
- **ERC-1056**: Ethereum community standard

### 3. Stability

**Stable / Production**

- **Deployed contracts**: On Ethereum mainnet since 2018
- **Battle-tested**: Years of production use
- **Active maintenance**: DIF and community support
- **Registry address**: `0xdca7ef03e98e0dc2b855be647c39abe984fcf21b`

## Special Considerations

### Design Patterns Worth Noting

- **Generative DIDs**: Any address is valid - no registration cost
- **Event-based history**: State reconstructed from events
- **Delegation with expiration**: Time-bounded key authorization
- **Minimal on-chain data**: Most info stored off-chain, referenced

### Limitations

- **Gas costs**: Updates require Ethereum transactions
- **Blockchain dependency**: Cannot resolve offline
- **Privacy**: On-chain events are public
- **Multi-chain complexity**: Different networks = different DIDs

## Recommendation

**No-go for direct support**

The did:ethr method cannot be supported because:

1. **Ethereum dependency**: Requires querying EVM blockchain state
2. **Cannot verify on-ledger**: Our nodes can't access Ethereum
3. **Cross-chain complexity**: Would need to support multiple EVM chains

This is explicitly mentioned in README.md:
> "did:ethr encodes the DID as a smart contract on an EVM blockchain... unless our ledger itself supports EVM contracts... we cannot support this DID method in a trustless manner"

**Design patterns to consider:**
- Generative DIDs (any key is valid without registration)
- Event-based state reconstruction
- Delegation with expiration
- Separation of identifier from on-chain features

These patterns could inform our own DID method design, even though we can't directly support did:ethr.
