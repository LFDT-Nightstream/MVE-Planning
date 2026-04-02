# did:peaq (peaq Decentralized Identifier)

| Property | Value |
|----------|-------|
| Specification | [peaq DID Specifications](https://github.com/peaqnetwork/peaq-did-specifications) |
| Organization | Peaq Technology GmbH / Peaq Network |
| DID Format | `did:peaq:<id-string>` where id-string uses base32 encoding (characters: 1-9, A-H, J-N, P-Z, a-k, m-z) |

## Overview

The did:peaq method is designed for machine-to-machine (M2M) identity in the Decentralized Physical Infrastructure Network (DePIN) ecosystem. It enables machines to discover each other, conduct transactions, verify claims, and maintain sovereignty. The method is built on the Substrate blockchain framework (peaq blockchain, part of the Polkadot ecosystem).

**DID Document Example:**
```json
{
  "id": "did:peaq:5HRNr4pXH7PYKEmeW1jzJVxepXyg8w2Q3YpgRNHpH8foNr5i",
  "controller": "did:peaq:5HRNr4pXH7PYKEmeW1jzJVxepXyg8w2Q3YpgRNHpH8foNr5i",
  "verificationMethod": [
    {
      "id": "#pk1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:peaq:5HRNr4pXH7PYKEmeW1jzJVxepXyg8w2Q3YpgRNHpH8foNr5i",
      "publicKeyMultibase": "z5HRNr4pXH7PYKEmeW1jzJVxepXyg8w2Q3YpgRNHpH8foNr5i"
    }
  ],
  "signature": {
    "type": "Ed25519VerificationKey2020",
    "issuer": "<did address of Peaq>",
    "hash": "<signature>"
  },
  "service": [
    { "id": "#payment", "type": "payment", "serviceEndpoint": "..." },
    { "id": "#p2p", "type": "p2p", "serviceEndpoint": "/ipv4/w.x.y.z/tcp/port/p2p-circuit/p2p/QmPublicKey" },
    { "id": "#metadata", "type": "metadata", "serviceEndpoint": "..." }
  ],
  "authentication": ["#pk1"]
}
```

**CRUD Operations** are performed via Substrate extrinsics on the peaq blockchain:
- **Create**: `createAttribute` with name (public key) and value (DID document)
- **Read**: `readAttribute` by address/public key
- **Update**: `updateAttribute` with modified DID document
- **Remove**: `removeAttribute` by identifier

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

The did:peaq method is fundamentally blockchain-dependent:

- **Resolution requires blockchain access**: DID documents are stored on the peaq blockchain using the `peaqDid` pallet. Reading a DID document requires calling the `readAttribute` extrinsic on the Substrate chain.
- **No self-certifying identifiers**: Unlike did:key or did:peer, the identifier (`5HRNr4pXH7PYKEmeW1jzJVxepXyg8w2Q3YpgRNHpH8foNr5i`) is a Substrate address/public key, but the full DID document with verification methods, services, and authentication references must be retrieved from the blockchain.
- **State-dependent verification**: To verify a DID, nodes would need to query the peaq blockchain to retrieve the current state of the DID document.

The specification does not provide any mechanism for offline or self-contained DID resolution.

### 2. Ecosystem

**Growing ecosystem, focused on DePIN/IoT:**

- **SDK Support**: JavaScript SDK and Python SDK available for building applications
- **Robotics SDK**: Launched September 2025 with ROS 2 compatibility
- **Adoption**: 3.35M+ onchain machines, 45.7M+ transactions, 30+ DePIN projects onboarded
- **Infrastructure**: 49,000 TPS demonstrated, EVM + ink! support
- **Use Cases**: Primarily focused on machine identity (vehicles, charging stations, IoT devices, robots)

The ecosystem is active but highly specialized for the DePIN/machine economy use case rather than general-purpose identity.

### 3. Stability

**Draft specification with minimal updates:**

- **Status**: Explicitly marked as "draft stage" in the specification
- **Repository Created**: January 2022
- **Last Specification Commit**: September 2022 (over 3 years ago)
- **Last Repository Activity**: December 2024 (metadata update)
- **Focus**: Originally designed for vehicle/charging station use cases with plans for generalization

The specification has seen no substantive updates since 2022, despite the network's active development. This suggests the spec may not be keeping pace with implementation changes.

## Special Considerations

1. **Machine-Focused Design**: Unlike most DID methods designed for human identity, did:peaq is specifically built for machine identity in the "Machine Economy" - IoT devices, robots, vehicles, etc.

2. **Substrate/Polkadot Ecosystem**: Tightly coupled to the Substrate framework and peaq blockchain, which is part of the Polkadot ecosystem.

3. **Service Endpoints**: The specification emphasizes service endpoints for payment (wallet addresses), P2P networking (libp2p), and metadata retrieval - all oriented toward machine-to-machine interaction.

4. **Cryptographic Support**: Supports Ed25519 and sr25519 (Schnorr signatures over Ristretto, commonly used in Substrate).

5. **Non-Standard Signature Field**: The DID document includes a `signature` field which is not part of the W3C DID Core specification.

## Recommendation

**No-go**

The did:peaq method cannot be supported in a system where nodes cannot make HTTP requests or access external blockchain state. Key reasons:

1. **Blockchain dependency is fundamental**: DID resolution requires querying the peaq blockchain's `peaqDid` pallet. There is no self-contained or offline resolution mechanism.

2. **No deterministic resolution**: Unlike did:key where the public key is encoded in the DID itself, did:peaq identifiers only contain the Substrate address - the full DID document must be fetched from on-chain state.

3. **Specification immaturity**: The spec remains in draft status with no updates in over 3 years, despite active network development. This creates uncertainty about long-term compatibility.

4. **Niche use case**: The method is specifically designed for machine/DePIN identity rather than general-purpose decentralized identity, limiting its applicability.

To support did:peaq, the system would need to either run a peaq blockchain node or make RPC calls to peaq network endpoints - both of which violate the constraint of no external dependencies.
