# did:op (Ocean Protocol Decentralized Identifiers)

| Property | Value |
|----------|-------|
| Specification | [OEP-7 v0.2](https://github.com/oceanprotocol/OEPs/blob/master/7/v0.2/README.md) (archived, current docs at [docs.oceanprotocol.com](https://docs.oceanprotocol.com/developers/identifiers)) |
| Organization | Ocean Protocol Foundation |
| DID Format | `did:op:<sha256-hash>` |

## Overview

The did:op method is Ocean Protocol's approach to decentralized identifiers for digital assets in their data marketplace ecosystem. It is designed to identify data assets (datasets, algorithms, workflows) that are traded on the Ocean Protocol network.

**How it works:**
1. A DID is computed as `sha256(address of ERC721 contract + chainId)` where the ERC721 contract represents a "Data NFT"
2. The DID resolves to a DID Document (DDO) containing asset metadata, services, and access information
3. The DDO is stored off-chain (in Ocean's Aquarius indexer) while only the DID and integrity checksum are stored on-chain
4. Resolution requires querying the Aquarius metadata service or reading blockchain events to locate the DDO

**DID Format Example:**
```
did:op:0ebed8226ada17fde24b6bf2b95d27f8f05fcce09139ff5cec31f6d81a7cd2ea
```

The identifier portion is a 64-character hex string representing a SHA-256 hash.

## Evaluation

### 1. Feasibility/Complexity

**Assessment: Not feasible without external dependencies**

The did:op method has fundamental requirements that make offline verification impossible:

- **Blockchain dependency**: DIDs are derived from ERC721 contract addresses and chain IDs. To verify a DID is valid and corresponds to a real asset, you must query an Ethereum-compatible blockchain (Ethereum mainnet, Polygon, etc.) to confirm the NFT contract exists
- **Resolution requires HTTP**: DDOs are stored off-chain in Aquarius (Ocean's metadata indexer). Resolving a DID to its document requires HTTP calls to Aquarius endpoints like `https://aquarius.oceanprotocol.com/api/v1/aquarius/assets/ddo/{did}`
- **Integrity verification requires blockchain**: The DDO checksum is stored in blockchain events. Verifying that a DDO hasn't been tampered with requires reading the on-chain checksum and comparing it to the computed DDO hash
- **No self-contained verification**: Unlike did:key or did:jwk, the DID string alone contains no cryptographic material - it's just a hash that must be looked up externally

### 2. Ecosystem

**Assessment: Moderate, domain-specific**

- **W3C Registration**: Registered in the W3C DID Method Registry with status "registered"
- **Libraries**: ocean.js (JavaScript) and ocean.py (Python) libraries support DID operations
- **Tooling**: Ocean Market provides a reference implementation; third-party utilities exist (e.g., rugpullindex/did-op for address conversion)
- **Adoption**: Used within Ocean Protocol's data marketplace ecosystem; partnerships with Daimler, BP Ventures; deployed on Ethereum and Polygon
- **Scope**: Primarily focused on data asset trading - not general-purpose identity

The ecosystem is healthy but narrowly focused on Ocean Protocol's data marketplace use case rather than general DID adoption.

### 3. Stability

**Assessment: Moderately stable but evolving**

- **Spec Version**: The original OEP-7 specification is at v0.2 (Draft status)
- **Repository Status**: The OEPs repository has been archived; specification moved to docs.oceanprotocol.com
- **Evolution**: The DDO specification has evolved significantly (now at version 4.1.0), with changes to required fields and structure
- **Breaking Changes**: The method for computing DIDs changed from earlier versions (previously based on asset ID, now based on NFT contract address + chainId)

The spec is actively maintained but has undergone significant changes, and the original specification location is now obsolete.

## Special Considerations

1. **Asset-centric, not identity-centric**: did:op is designed for identifying data assets, not people or organizations. The "subject" of a did:op DID is always a data asset represented by an NFT.

2. **Multi-chain support**: DIDs include chain ID in their computation, allowing the same asset concept to exist across different EVM chains (Ethereum, Polygon, etc.)

3. **Compute-to-Data**: Ocean Protocol's unique feature allows algorithms to run on encrypted data. The DDO contains service definitions for these compute operations.

4. **State management**: Assets have 6 possible states (Active, End-of-life, Deprecated, Revoked, Ordering Disabled, Unlisted) affecting discoverability and access.

5. **Token-gated access**: Access to assets requires holding "datatokens" (ERC20 tokens) plus optional credential verification.

## Recommendation

**No-go**

The did:op method is fundamentally incompatible with our constraint that nodes cannot make HTTP requests or access external blockchain state to validate DIDs:

1. **DID creation requires blockchain**: You must deploy an ERC721 contract to create a valid did:op
2. **DID resolution requires HTTP**: DDOs are stored in Aquarius, an HTTP-accessible metadata service
3. **Verification requires blockchain**: Integrity checksums are stored in on-chain events
4. **No cryptographic self-verification**: The DID string is just a hash with no embedded keys or signatures

Unlike self-describing methods (did:key, did:jwk) where the DID itself contains verifiable cryptographic material, did:op DIDs are essentially opaque pointers into Ocean Protocol's infrastructure. Supporting did:op would require either:
- Running an Ocean Protocol node with blockchain access
- Maintaining a synchronized cache of all Ocean Protocol assets
- Making real-time HTTP/RPC calls for every DID operation

None of these approaches are compatible with a system that must validate DIDs without external dependencies.
