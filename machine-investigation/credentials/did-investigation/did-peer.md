# did:peer (Peer DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:peer Spec](https://identity.foundation/peer-did-method-spec/) |
| Organization | Decentralized Identity Foundation (DIF) |
| DID Format | `did:peer:<numalgo><encoded-data>` |

## Overview

The did:peer method enables peer-to-peer decentralized identifiers without any blockchain or central registry. The "registry" is the synchronization protocol between peers - each party stores DIDs they've received locally. This is designed for private, pairwise relationships.

Example DIDs:
- Method 0: `did:peer:0z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK`
- Method 2: `did:peer:2.Ez6LSbysY2xFMRpGMhb7tFTLMpeuPRaqaWM1yECx2AtzE3KCc...`

### DID Structure

- **Prefix**: `did:peer:`
- **Numalgo**: Single digit indicating generation method (0-4)
- **Encoded data**: Method-specific encoded key/document material

### Numalgo Types

| Method | Description |
|--------|-------------|
| 0 | Single inception key (functionally equivalent to did:key) |
| 1 | Genesis document hash |
| 2 | Multiple inception keys + services encoded inline |
| 3 | SHA256-shortened Method 2 (for messaging efficiency) |
| 4 | Statically resolvable with short/long forms |

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate via chosen numalgo, share with peer |
| Read | Resolve from local storage (no public resolution) |
| Update | **Not supported** - use DID Rotation instead |
| Delete | Remove from local storage |

## Evaluation

### 1. Feasibility/Complexity

**Partially feasible - numalgo 0 and 2 are interesting**

The did:peer method has varying applicability:

**Numalgo 0**: Functionally equivalent to did:key - fully self-contained, we could support this.

**Numalgo 2**: Encodes multiple keys and services directly in the DID - self-contained, could be supported.

**Numalgo 1, 3, 4**: Require local storage or genesis document access - not suitable for stateless verification.

**Key consideration**: did:peer is designed for pairwise relationships, not public identity. The "registry" is each peer's local storage, which doesn't align with our public ledger model.

**Adaptation possibility**:
- Numalgo 0: Easy (same as did:key)
- Numalgo 2: Moderate (decode inline keys/services)
- Others: Low (require external state)

### 2. Ecosystem

**Strong in specific contexts**

- **DIF specification**: Decentralized Identity Foundation standard
- **Hyperledger Aries**: Primary use in Aries ecosystem
- **DIDComm**: Standard for DIDComm messaging
- **Privacy focused**: Designed for private interactions
- **Active development**: Ongoing specification work

### 3. Stability

**Stable but evolving**

- **Multiple numalgo versions**: Added over time
- **DIF maintenance**: Institutional backing
- **Aries adoption**: Production use in enterprise contexts
- **Method 2 prevalent**: Most commonly used variant

## Special Considerations

### Design Philosophy

- **No public registry**: Intentionally avoids central/blockchain infrastructure
- **Pairwise relationships**: Each relationship gets unique DIDs
- **Local-first**: Works completely offline
- **Privacy maximized**: Peers only know what they're told

### Key Rotation

- **No in-place updates**: Cannot modify existing peer DIDs
- **DID Rotation**: Create new DID, notify peers via protocol
- **Protocol-dependent**: Relies on DIDComm or similar for rotation messages

### Trust Model

- **Inception key entropy**: Security from initial key generation
- **No MITM at creation**: Proper generation verifiable
- **Peer verification**: Each party validates received DIDs

## Recommendation

**Requires investigation - partial support possible**

The did:peer method has nuanced applicability:

**Could support:**
- **Numalgo 0**: Equivalent to did:key, trivial to support
- **Numalgo 2**: Self-contained multiple keys/services, moderate effort

**Cannot support well:**
- **Numalgo 1, 3**: Require genesis document or external state
- **Rotation**: Would need additional protocol support

**Considerations:**
1. did:peer is designed for private pairwise relationships, not public registry
2. Users wanting peer DIDs likely want the full pairwise privacy model
3. Supporting partial numalgo might confuse users

**Recommendation path:**
1. Support numalgo 0 and 2 if there's demand for peer DIDs
2. Document that our use is "peer DID format" not full peer protocol
3. If enterprise users need multi-key DIDs, numalgo 2 parsing could be valuable
4. Consider whether did:key already covers our needs (numalgo 0 adds no value over did:key)

For the MVP, focusing on did:key is likely sufficient. did:peer:2 could be added later if multi-key inline encoding is valuable for our use cases.
