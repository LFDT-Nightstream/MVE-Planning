# did:dht (DHT DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:dht Spec](https://did-dht.com/) |
| Organization | DIF (Decentralized Identity Foundation) |
| DID Format | `did:dht:<z-base-32-encoded-pubkey>` |

## Overview

The did:dht method uses the Mainline DHT (BitTorrent's distributed hash table, BEP44) for storing and resolving DIDs. It leverages Ed25519 keys for self-certifying identifiers that can be independently verified without trusting any specific server.

Example DID: `did:dht:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK`

### DID Structure

- **Prefix**: `did:dht:`
- **Identifier**: z-base-32 encoded Ed25519 public key (Identity Key)

### Key Features

- **Self-certifying**: All DHT records are authenticated via BEP44 signatures
- **Independently verifiable**: Clients can verify records without trusting servers
- **Gateway infrastructure**: Optional gateway servers for retention and discovery
- **Ed25519 required**: Mainline DHT only supports Ed25519 signatures

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate Ed25519 key, encode as DID, publish to DHT |
| Read | Query DHT or gateway for DID document |
| Update | Republish with new document (signed by Identity Key) |
| Deactivate | Let DHT record expire or publish deactivation |

### Key Rotation

- **Verification methods**: Can be rotated via update operations
- **Identity Key**: Cannot be rotated (inherent to Mainline constraints)
- **DID rotation**: Create new DID with cryptographic link to old one

## Evaluation

### 1. Feasibility/Complexity

**Interesting approach but depends on DHT infrastructure**

The did:dht method has unique characteristics:

**Positive aspects:**
- **Self-certifying**: Records are cryptographically signed, independently verifiable
- **No blockchain**: Uses existing DHT infrastructure (BitTorrent Mainline)
- **Ed25519 based**: Same key type we'd likely support for did:key
- **Decentralized**: No single point of failure in theory

**Challenges:**
- **DHT infrastructure required**: Must access Mainline DHT network
- **Gateway dependency**: Practical use often requires gateway servers
- **DHT ephemerality**: Records need republishing to persist
- **Ed25519 only**: Limited to single algorithm

**Adaptation possibility**: Low for direct support. Our ledger nodes can't query the Mainline DHT. However, we could:
1. Support similar self-certifying approach with our own storage
2. Consider DHT as client-side resolution option
3. Use Ed25519-based self-certifying pattern (but this is essentially did:key)

### 2. Ecosystem

**Growing / DIF-backed**

- **DIF specification**: Decentralized Identity Foundation
- **Multiple implementations**: Go, TypeScript, Kotlin, Swift, Dart, Rust
- **Active development**: Reference gateway server available
- **TBD Web5**: Part of TBD's Web5 initiative

### 3. Stability

**Early but active**

- **Active specification**: Ongoing development
- **Apache 2.0 license**: Open source
- **Gateway registry**: Infrastructure for discovery
- **BEP44 foundation**: Built on proven DHT standard

## Special Considerations

### Self-Certifying Design

The key insight from did:dht:
> "Trust in a specific Mainline or Gateway server for providing unaltered messages is unnecessary. Instead, all clients have the ability to verify messages themselves."

This is similar to did:key but with updateable documents via DHT.

### DHT Characteristics

- **Ephemeral storage**: Records expire if not republished
- **Global network**: Leverages existing BitTorrent infrastructure
- **No fees**: No transaction costs for publishing
- **Eventual consistency**: DHT propagation takes time

### Gateway Infrastructure

- **Retention**: Gateways republish records to reduce controller burden
- **Discovery**: Gateway registries enable finding servers
- **Optional**: Can interact directly with DHT

## Recommendation

**No-go for direct support, but design pattern is valuable**

The did:dht method cannot be directly supported because:

1. **DHT infrastructure required**: Nodes can't query Mainline DHT
2. **Gateway dependency**: Practical use requires external servers
3. **Similar to did:key**: Ed25519-based self-certifying - already covered by did:key

**However, the self-certifying principle is important:**
- Records are independently verifiable regardless of source
- No need to trust the resolver/server
- Client-side verification of cryptographic proofs

This aligns well with did:key's approach and validates our focus on self-verifying DID methods.

**Alternative consideration:**
If we need updateable DIDs without blockchain, the DHT approach shows it's possible with self-certifying records. Our ledger could serve a similar purpose to gateways - providing durable storage for self-certifying DID documents.
