# did:near (NEAR DID Method)

| Property | Value |
|----------|-------|
| Specification | [DID-Method-NEAR.md](https://github.com/ontology-tech/DID-spec-near/blob/master/NEAR/DID-Method-NEAR.md) |
| Organization | Ontology Tech (spec author), DTI-web3 (implementation) |
| DID Format | `did:near:<near-account-id>` (e.g., `did:near:alice.near`) |

## Overview

The NEAR DID method enables decentralized identifiers on the NEAR blockchain. It leverages NEAR's human-readable account system (e.g., `alice.near`) as the basis for DIDs. The method supports two resolution strategies:

1. **Named Account DIDs**: Resolve directly from on-chain account keys (e.g., `did:near:alice.testnet`)
2. **Registry DIDs**: Use smart contract registries for Base58 public key identifiers (e.g., `did:near:CF5RiJYh4EVmEt8UADTjoP3XaZo1NPWxv6w5TmkLqjpR`)

DID documents are stored and managed via a NEAR DID management smart contract ("the registry"). CRUD operations are performed through smart contract method calls:
- **Create**: `reg_did_using_account()` - creates a DID tied to the caller's NEAR account
- **Read**: `get_document()` - retrieves the full DID document from the registry
- **Update**: `add_controller()` / `remove_controller()` - manages DID controllers
- **Delete**: `deactivate_did()` - permanently deactivates a DID (cannot be reactivated)

The specification uses Ed25519 and EcdsaSecp256k1 verification keys in Base58 encoding.

## Evaluation

### 1. Feasibility/Complexity

**Assessment: Not feasible without external dependencies**

The did:near method is fundamentally dependent on NEAR blockchain state:

- **Resolution requires blockchain access**: Resolving a DID document requires calling `get_document()` on the NEAR registry smart contract, which necessitates NEAR RPC node access
- **No self-certifying identifiers**: Unlike did:key or did:peer, the DID string itself does not encode cryptographic material; it only references a NEAR account name
- **State-dependent verification**: The public keys and authentication methods are stored on-chain and can be updated at any time, meaning the DID document is mutable and must be fetched in real-time
- **Registry dependency**: Both named account DIDs and registry DIDs require querying NEAR blockchain state to obtain the DID document

Nodes cannot verify did:near DIDs without making HTTP calls to NEAR RPC endpoints or having direct blockchain access.

### 2. Ecosystem

**Assessment: Limited adoption, minimal tooling**

- **NEAR Protocol** itself is a well-established blockchain with ~46 million monthly active users and 2,500+ developers (as of 2025)
- However, **did:near specifically** has minimal adoption:
  - The ontology-tech specification repository has not been updated since August 2020
  - Reference implementations exist (DTI-web3/did-near, did-near-resolver) but with limited community traction
  - No evidence of widespread DID/verifiable credential adoption on NEAR
- **Available SDKs**:
  - DID Registry Contract: github.com/DTI-web3/did-near
  - Resolver SDK: github.com/DTI-web3/did-near-resolver
  - ProofType SDK: NPM @kaytrust/prooftypes

### 3. Stability

**Assessment: Abandoned/Stale**

- **Last specification update**: August 2, 2020 (over 5 years ago)
- **Spec status**: Marked as "Proposal" (NEP PR #0000) - never formally adopted
- **No recent development activity** on the specification repository
- The specification references W3C DID v1.0 but predates many modern DID method conventions
- NEAR's official documentation (docs.near.org/primitives/did) references DIDs but the ecosystem tooling appears fragmented

## Special Considerations

- **Human-readable identifiers**: NEAR's account naming system allows memorable DIDs like `did:near:alice.near`, which is user-friendly but ties identity to the account namespace
- **One DID per account**: The current implementation restricts each NEAR account to a single DID
- **Permanent deletion**: Deactivated DIDs cannot be re-registered or reactivated, which is a strong security feature but also a risk if done accidentally
- **64-character limit**: The namespace-specific identifier is limited to 64 characters
- **Controller delegation**: DIDs can have multiple controllers with equivalent privileges, which introduces governance complexity

## Recommendation

**No-go**

The did:near method cannot be supported in our system due to the fundamental constraint that nodes cannot make HTTP requests to validate DIDs. Specifically:

1. **Resolution requires NEAR blockchain access**: There is no way to verify a did:near DID without querying the NEAR blockchain (either via RPC or direct node access). The DID string itself contains only an account reference, not cryptographic material.

2. **Mutable state**: Unlike self-certifying methods (did:key, did:peer), did:near documents are stored on-chain and can change. Any cached resolution could become stale.

3. **Ecosystem concerns**: The specification has been abandoned since 2020, never achieved formal NEP status, and has minimal adoption even within the NEAR ecosystem.

If NEAR-based identity is required, consider alternative approaches such as using did:key with NEAR account keys exported as the cryptographic material, which would allow offline verification while still maintaining a connection to NEAR accounts.
