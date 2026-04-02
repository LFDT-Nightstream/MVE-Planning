# did:future (Future DID Method)

| Property | Value |
|----------|-------|
| Specification | [Future DID Spec](https://github.com/netease-chain/Future-DID-Method-Specification/blob/main/README.md) |
| Organization | Netease Blockchain Team |
| DID Format | `did:future:<0x40-char-hex-address>` |

## Overview

Future DID is a decentralized identity system created by Netease Blockchain Team. It uses an event-chain storage model where modifications are stored as blockchain receipt events rather than traditional contract storage.

Example DID: `did:future:0x64db3dd38333a852d3783f8da5c9f9c15926174c`

### DID Structure

- **Prefix**: `did:future:`
- **Identifier**: 0x + 40 hexadecimal characters (Ethereum-style address)

### Key Features

- **Event-chain storage**: Modifications as blockchain events
- **secp256k1**: Key generation algorithm
- **Version tracking**: Document version numbers
- **Timestamps**: Created/updated fields

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate account via SDK → record on blockchain |
| Read | Query block → retrieve receipts → reconstruct from events |
| Update | `setAttribute()` contract method |
| Revoke | `setAttribute()` with revocation data |

### Supported Algorithms

- EcdsaSecp256k1VerificationKey2019

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:future method requires:

- **Netease blockchain**: Proprietary blockchain infrastructure
- **Event reconstruction**: Resolution requires scanning blockchain events
- **Chinese infrastructure**: Netease's internal blockchain
- **SDK dependency**: Netease's blockchain SDK

**Adaptation possibility**: Very low. Tied to Netease's proprietary blockchain.

### 2. Ecosystem

**Limited / Single vendor**

- **Netease**: Chinese tech giant (gaming, cloud)
- **Internal use**: Future Conference 2020, Netease Star App
- **v0.1**: Early stage specification
- **December 2020**: Relatively recent

### 3. Stability

**Early stage**

- **v0.1**: Initial version
- **Netease backing**: Corporate support
- **Government/enterprise target**: Specific use cases

## Special Considerations

### Event-Chain Model

Rather than storing full documents in contract storage, modifications are recorded as blockchain events. This is:
- More scalable (no large mapping tables)
- Requires event scanning for reconstruction
- Similar to event sourcing pattern

### Use Cases

- Government scenarios
- Enterprise identity
- Netease's internal applications

## Recommendation

**No-go**

The did:future method is unsuitable for our use case:

1. **Netease blockchain dependency**: Proprietary infrastructure
2. **Chinese market focus**: Limited international applicability
3. **Event scanning**: Complex resolution process
4. **Single vendor**: No path to independent operation

The event-chain storage pattern is interesting for scalability but the proprietary blockchain requirement is prohibitive.
