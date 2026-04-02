# did:prism (PRISM/Identus DID Method)

| Property | Value |
|----------|-------|
| Specification | [PRISM Method Spec](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md) |
| Organization | IOG (Input Output Global) / Hyperledger Identus |
| DID Format | `did:prism:<hex-hash>` |

## Overview

The did:prism method is a decentralized identifier system built on the Cardano blockchain. Originally developed by IOG as Atala PRISM, it was contributed to Hyperledger Foundation as Hyperledger Identus in 2023. It supports key rotation, multiple key types with different purposes, and service endpoints.

Example DID: `did:prism:9b5118411248d9663b6ab15128fba8106511230ff654e7514cdcc4ce919bde9b`

### DID Structure

- **Prefix**: `did:prism:`
- **Identifier**: 64-character hexadecimal hash of initial state
- **Unpublished**: Append encoded state after colon for unpublished DIDs

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | `CreateDIDOperation` with keys and services (can be off-chain initially) |
| Read | Query PRISM nodes that maintain DID→document map |
| Update | `UpdateDIDOperation` to add/remove keys, modify services |
| Deactivate | `DeactivateDIDOperation` permanently marks DID as inactive |

### Key Types and Purposes

- **master**: Primary control key
- **issuing**: For issuing credentials
- **authentication**: For authentication
- **keyAgreement**: For encryption/key exchange
- **revocation**: For revoking credentials
- **capabilityInvocation/Delegation**: For authorization

Maximum 50 active keys per DID.

### Supported Algorithms

- **Curves**: secp256k1, Ed25519, X25519
- **Signature**: SHA256 with ECDSA
- **Verification Method**: JsonWebKey2020

## Evaluation

### 1. Feasibility/Complexity

**Potentially adaptable - Requires investigation**

The did:prism method has interesting properties:

**Cardano-specific aspects:**
- Operations embedded in Cardano transaction metadata
- PRISM nodes read Cardano blockchain
- 112-block confirmation depth for finality

**Potentially adaptable aspects:**
- Protocol Buffer serialization is portable
- Operation structure (Create, Update, Deactivate) is well-designed
- Key rotation and multi-key support are valuable features

**Adaptation possibility**: Moderate. We could potentially:
1. Use the same operation structure and serialization
2. Anchor operations to our ledger instead of Cardano
3. Implement PRISM-compatible node logic for our ledger

However, this would be a new DID method inspired by PRISM, not direct `did:prism` support (which requires Cardano).

### 2. Ecosystem

**Strong in Cardano ecosystem**

- **Hyperledger Foundation**: Now part of Hyperledger Identus
- **IOG backing**: Major corporate sponsor
- **Real deployments**: 5M+ student DIDs in Ethiopia pilot
- **Enterprise focus**: Designed for large-scale institutional use
- **DIDComm V2**: Supports modern messaging protocols
- **Multiple credential formats**: JWT-VC, AnonCreds in progress

### 3. Stability

**Stable / Production**

- **W3C compliant**: Designed to DID Core specification
- **Hyperledger governance**: Foundation oversight
- **Active development**: Identus project ongoing
- **Battle-tested**: Large-scale deployments

## Special Considerations

### Design Patterns Worth Noting

- **Purpose-specific keys**: Different keys for different operations (master, issuing, auth, etc.)
- **Unpublished DIDs**: Can create DIDs off-chain, publish later
- **Batched operations**: Multiple ops in single transaction (AtalaBlock)
- **Key limits**: Maximum 50 active keys per DID

### Hyperledger Identus

The open-source version (formerly Open Enterprise Agent) provides:
- DIDComm V2 messaging
- Credential issuance/verification
- Protocol implementations

## Recommendation

**Requires investigation - Design patterns valuable**

The did:prism method itself requires Cardano, but the design is worth studying:

1. **Cannot directly support did:prism**: Requires Cardano blockchain
2. **Operation design is excellent**: Well-thought-out key management
3. **Purpose-specific keys**: Valuable pattern for enterprise use
4. **Could inspire our design**: Similar operation structure possible

**Investigation paths:**
- Study Identus/PRISM operation structure for our own method
- Evaluate if we want multi-purpose key separation
- Consider whether our method should support 50+ keys per DID

**Connection to Identus ecosystem:**
If we design our DID method with similar semantics, we might enable easier bridging between our system and Identus-based deployments, which is relevant given the Cardano ecosystem connection.

This method is particularly relevant if our registry will be used alongside Cardano-based systems.
