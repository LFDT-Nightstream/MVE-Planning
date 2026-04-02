# did:ockam (Ockam DID Method)

| Property | Value |
|----------|-------|
| Specification | [Ockam DID Method Specification](https://github.com/build-trust/did-method-spec/blob/master/README.md) |
| Organization | [Ockam / build-trust](https://github.com/build-trust) |
| DID Format | `did:ockam:[zone1:zone2:...]<idstring>` where idstring is 28-31 base58-encoded characters |

## Overview

The did:ockam method was designed for the Ockam Network, originally conceived as a blockchain-based identity system for IoT devices. The DID identifier is derived from a public key through a deterministic process:

1. Generate a public/private keypair using W3C-approved cryptographic methods
2. Hash the public key using a multihash-supported algorithm
3. Truncate the hash to the lower 20 bytes
4. Prepend the multihash algorithm prefix
5. Base58 encode (Bitcoin alphabet) the result

The format supports optional hierarchical "zones" for organizational namespacing (e.g., `did:ockam:us:east:2PCd14L1pLMpfSfpgKe2HyYZFu2pf`).

DID documents are managed through Verifiable Claims submitted as transactions to the "Ockam Network." CRUD operations (Create, Read, Update, Delete) all require cryptographic proofs where the issuer and subject of claims must match the DID being operated on.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

The specification explicitly requires interaction with the "Ockam Network" for all operations:
- **Create**: "Ockam Clients can create/register an entity in the Ockam Network by submitting a Verifiable Claim as a transaction"
- **Read/Resolve**: "Ockam Clients can read a DID document by sending a query request for a DID"
- **Update/Delete**: Both require submitting transactions to the network

The specification does not provide any mechanism for offline or self-contained verification. While the DID identifier itself is derived from a public key (similar to did:key), the DID document containing authentication methods, service endpoints, and other metadata must be retrieved from the Ockam Network. The specification lacks details on:
- Network topology and node requirements
- How resolution queries are processed
- Whether the network is blockchain-based or uses another consensus mechanism
- Any verifiable data structures that could enable offline verification

### 2. Ecosystem

**Minimal ecosystem presence.**

- The specification repository has only 16 stars and 6 forks
- A basic Go implementation exists ([ockam-network/did](https://github.com/ockam-network/did)) but appears dormant
- No evidence of third-party implementations or integrations
- Not listed in the W3C DID Method Registry as a mature method
- Ockam as a company has pivoted away from blockchain-based identity toward secure communication protocols (end-to-end encryption, mutual authentication)

### 3. Stability

**Abandoned/stale specification.**

- The specification is explicitly marked as "a work in progress draft"
- Last commit to the specification repository: January 2021 (over 4 years ago)
- The repository has 0 open issues and 0 pull requests, indicating no active development
- Ockam's main product ([build-trust/ockam](https://github.com/build-trust/ockam)) has evolved significantly and no longer appears to reference or use did:ockam in its current architecture
- The original "Ockam Network" that this DID method was designed for does not appear to exist as described

## Special Considerations

- **Interesting design**: The zone hierarchy concept (`did:ockam:zone1:zone2:idstring`) is a unique feature that could allow organizational namespacing, but it adds complexity without clear benefit over simpler methods
- **Orphaned specification**: The DID method appears to be an artifact of Ockam's earlier blockchain-focused strategy (circa 2018-2019) that was never completed as the company pivoted
- **No reference implementation**: The "Ockam Network" described in the specification does not appear to be operational or publicly accessible
- **Trail of Bits audit (2024)**: While Ockam's current cryptographic design received a positive security audit, this audit focused on their secure channel protocols, not the did:ockam method

## Recommendation

**No-go**

The did:ockam method cannot be supported for the following reasons:

1. **External network dependency**: Resolution requires querying the "Ockam Network," which violates the constraint that nodes cannot make HTTP requests to validate DIDs
2. **Non-existent infrastructure**: The Ockam Network described in the specification does not appear to exist as a public, queryable service
3. **Abandoned specification**: No updates in over 4 years, still marked as draft, with no apparent path to completion
4. **Company pivot**: Ockam has moved away from blockchain-based identity toward secure communication protocols, making it unlikely this DID method will ever be completed or maintained
5. **Zero adoption**: No evidence of real-world usage or third-party implementations

Even if the specification were completed, the fundamental design requiring network queries for resolution makes it incompatible with systems that cannot access external blockchain state or make HTTP requests.
