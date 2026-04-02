# did:abt (ArcBlock DID Method)

| Property | Value |
|----------|-------|
| Specification | [ABT DID Spec](https://arcblock.github.io/abt-did-spec/) |
| Organization | ArcBlock |
| DID Format | `did:abt:<base58-encoded-identifier>` |

## Overview

The ABT DID method is a decentralized identifier system created by ArcBlock. It encodes cryptographic algorithm metadata directly into the DID string itself (similar to did:key) while also supporting on-chain state management for features like revocation.

Example DID: `did:abt:z1muQ3xqHQK2uiACHyChikobsiY5kLqtShA`

### DID Structure

The method-specific identifier is a Base58-encoded binary value containing:
- **DID type bytes** (2 bytes): Encodes role type, key algorithm, and hash function
- **Public key hash** (20 bytes): Hash of the public key
- **Checksum** (4 bytes): For validation

The first two bytes encode:
- Bits 0-5: Role Type (account=0, application=3, etc.)
- Bits 6-10: Key Type algorithm (ED25519=0, secp256k1=1)
- Bits 11-15: Hash function (sha3=1, sha2=6, etc.)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate keypair, hash public key, prepend type bytes, add checksum, Base58 encode |
| Read | Query via GRPC to ABT network with account address |
| Update | Transactions create new state versions (old versions preserved on chain) |
| Deactivate | `RevokeTx` marks account as dead (irreversible) |

### Supported Algorithms

**Key Generation:**
- ED25519
- secp256k1

**Hash Functions:**
- keccak, sha3, keccak_384, sha3_384, keccak_512, sha3_512, sha2

## Evaluation

### 1. Feasibility/Complexity

**Medium complexity / Partially feasible**

The did:abt method has two distinct aspects:

1. **Self-describing identifier**: The DID string itself encodes the key type and hash algorithm, similar to did:key. This part is straightforward to support - we can parse the DID to extract the public key hash and verify signatures.

2. **On-chain state**: The method relies on the ABT blockchain for state management (account status, revocation). This requires either:
   - Running ABT network nodes (not feasible for our use case)
   - Treating did:abt as a "did:key-like" method without state features

**Adaptation possibility**: Moderate for basic verification (treating it like did:key), but low for full functionality. The self-describing nature means we could verify signatures without network access, but we couldn't support revocation checking or state queries.

**Key limitation**: Unlike did:key, did:abt only contains a *hash* of the public key, not the full public key. This means the user must provide the full public key separately for verification, and we can only verify it matches the hash in the DID.

### 2. Ecosystem

**Niche / Limited**

- **Single vendor**: Primarily used within the ArcBlock ecosystem
- **Universal Resolver support**: Has a driver in the DIF Universal Resolver
- **Wallet support**: ArcBlock provides their own DID Wallet
- **Limited third-party adoption**: Not widely adopted outside of ArcBlock's own products
- **Active development**: ArcBlock continues to develop their platform, but did:abt remains niche

### 3. Stability

**Uncertain**

- **Incomplete specification**: Several sections marked as "TBD" including registry blockchain details and revoke authentication
- **No version history**: Specification lacks clear versioning or changelog
- **Examples dated 2019**: JWT examples in spec contain 2019 timestamps, unclear how actively maintained
- **Proprietary evolution risk**: As a single-vendor specification, changes are at ArcBlock's discretion

## Special Considerations

- **Privacy features**: Designed with privacy in mind - supports application-specific DIDs derived from a master DID using HD derivation, reducing correlation across applications
- **No key rotation**: While it supports HD key derivation for generating multiple DIDs, there's no mechanism to rotate keys for an existing DID
- **Public key not embedded**: Unlike did:key, the DID only contains a hash, requiring out-of-band public key transmission

## Recommendation

**No-go**

While did:abt has some interesting design choices (self-describing algorithm metadata, privacy-focused HD derivation), it has significant drawbacks for our use case:

1. **Blockchain dependency**: Full functionality requires access to ABT network
2. **Limited ecosystem**: Single-vendor solution with minimal third-party adoption
3. **Incomplete specification**: Key sections still marked TBD
4. **Hash-only identifier**: Unlike did:key, requires out-of-band public key transmission

The self-describing algorithm encoding is interesting but doesn't provide enough benefit over did:key (which embeds the full public key) to justify adoption. If we want algorithm-agnostic DIDs with embedded key information, did:key or did:jwk are better standardized choices.
