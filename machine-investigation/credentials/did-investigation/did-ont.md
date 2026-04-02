# did:ont (Ontology DID Method)

| Property | Value |
|----------|-------|
| Specification | [DID-ONT-method.md](https://github.com/ontio/ontology-DID/blob/master/docs/en/DID-ONT-method.md) |
| Organization | Ontio (Ontology Foundation) |
| DID Format | `did:ont:<base58-encoded-id>` |

## Overview
The Ontology DID Method is a decentralized identification protocol built on the Ontology blockchain. DIDs are managed through a native smart contract at address `0x0000000000000000000000000000000000000003` written in Go. The method supports identity registration, resolution, key management, and deactivation operations.

DID identifiers use base58 encoding (as originally defined by Bitcoin). An example DID looks like: `did:ont:AGsL32ZMvAwxYRN9Sv4mrgu3DgBSvTm5vt`

Key operations include:
- **Create**: Register DIDs via `RegIdWithPublicKey()` function with a public key
- **Read**: Resolve DID documents via `GetDDO()` function returning JSON with public keys and authentication elements
- **Update**: Modify keys via `AddKey()` and `RemoveKey()` functions (removed keys cannot be re-added)
- **Delete**: Remove all public keys to deactivate (permanent and irreversible)

The method supports ECDSA (secp224r1, secp256r1, secp384r1, secp521r1), SM2 (sm2p256v1), and EdDSA (ed25519) cryptographic algorithms.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible without external dependencies.**

The did:ont method is entirely dependent on the Ontology blockchain for all operations. DID resolution requires querying the blockchain's smart contract via the `GetDDO()` function. The specification explicitly notes that clients "could ask sufficient number of nodes and compare these return values" for verification, confirming that HTTP calls to Ontology nodes are required for any DID resolution.

There is no self-certifying or self-describing aspect to did:ont identifiers - the base58 identifier alone does not contain the cryptographic material needed to verify ownership. All verification data must be retrieved from the Ontology blockchain state.

### 2. Ecosystem
**Limited ecosystem.**

- The ontology-DID repository has modest engagement (67 stars, 25 forks, 10 contributors)
- SDKs available in Java and TypeScript
- No official releases tagged despite being at "Version 0.7.0"
- The main Ontology blockchain project is more active (852 stars, 297 forks, 58+ contributors) with recent releases (v3.0.0 in November 2025)
- Limited adoption outside the Ontology ecosystem

### 3. Stability
**Moderate stability.**

- The Ontology blockchain itself is actively maintained with 63 releases and recent updates
- The DID specification repository shows limited recent activity
- The spec references W3C DID requirements but specific version/date information is not provided
- The underlying blockchain supports multiple consensus algorithms (VBFT/DBFT/RBFT/SBFT/PoW) with 1-30 second block times

## Special Considerations
- **Permanent deletions**: Once a DID is deactivated by removing all keys, it cannot be reactivated
- **Key removal is permanent**: Removed public keys cannot be added back to the DID document
- **Chinese cryptography support**: Includes SM2 algorithm (Chinese national cryptographic standard) which may be relevant for certain compliance scenarios
- **Native smart contract**: DID operations are handled by a built-in Go smart contract, not a user-deployed contract

## Recommendation
**No-go**

The did:ont method requires access to Ontology blockchain state for all DID resolution and verification operations. Since nodes cannot make HTTP requests to external systems and we cannot access external blockchain state, there is no way to verify did:ont DIDs without running Ontology infrastructure or making network calls to Ontology nodes. The DID identifier itself is not self-certifying and contains no embedded cryptographic material that would allow offline verification.
