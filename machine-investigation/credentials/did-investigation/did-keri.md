# did:keri (KERI - Key Event Receipt Infrastructure)

| Property | Value |
|----------|-------|
| Specification | [Web of Trust](https://weboftrust.github.io/did-keri/) |
| Organization | Decentralized Identity Foundation (DIF) / Web of Trust |
| DID Format | `did:keri:<self-certifying-identifier>` |

## Overview
KERI DID is a ledger-agnostic decentralized identifier method built on the Key Event Receipt Infrastructure (KERI) protocol. Unlike most DID methods, KERI does not require any specific blockchain or distributed ledger. Instead, it uses self-certifying identifiers derived from cryptographic key material, with Key Event Logs (KELs) providing an append-only history of key state changes.

## Evaluation

### 1. Feasibility/Complexity
**Potentially self-contained with flexible infrastructure options.** Key characteristics:

- **Self-certifying identifiers**: The DID is cryptographically bound to inception keys, requiring no external registry for basic identity verification
- **Key Event Logs (KEL)**: Hash-chained, append-only data structure that can be stored and transmitted through various mechanisms
- **Ledger-agnostic by design**: The specification explicitly states it operates independently of any blockchain
- **Flexible KEL discovery**: Methods include distributed hash tables, ledger anchoring, witness network gossip, or direct exchange

Resolution process:
1. Locate the Key Event Log for the identifier prefix
2. Process the KEL into Key State following KERI validation semantics
3. Generate DID Document with verification methods for each key
4. Include Key State as DID Document Metadata

**Critical insight**: While KEL discovery mechanisms are "outside the scope of this specification," KERI can operate with direct peer-to-peer exchange of KELs, making it potentially self-contained for specific use cases.

### 2. Ecosystem
**Strong and growing ecosystem:**
- Developed by DIF with contributions from Jolocom, Spruce, Scoir, and others
- Dr. Sam Smith (ProSapien) as lead editor, a recognized authority in decentralized identity
- Active development with KERI v2.5.9 as the foundation
- Multiple implementations across different programming languages
- Gaining adoption in enterprise identity solutions

### 3. Stability
**High stability with formal specification:**
- Built on well-defined cryptographic principles
- Pre-rotation mechanism protects against key compromise
- Multi-signature support and witness configuration for enhanced security
- Agreement Algorithm for Control Establishment (KAACE) ensures non-repudiable attribution
- Duplicity detection prevents man-in-the-middle attacks
- Privacy-focused with entropy-based root-of-trust

**Key management features:**
- Key rotation without exposing next public keys (pre-rotation)
- Delegation capabilities for authority distribution
- Witness configuration for distributed validation
- Deactivation by rotating to zero controlling keys

## Technical Deep Dive

### Self-Certifying Identifiers
The identifier itself proves control through cryptographic binding to inception keys. This eliminates the need for external verification at the basic level.

### Key Event Log Structure
- **Inception Event**: Creates the identifier with initial key configuration
- **Rotation Events**: Change keys, thresholds, or witness configurations
- **Interaction Events**: Non-establishment events for other operations
- **Receipt Events**: Witness acknowledgments creating additional proof

### Security Properties
- Replay attack resistance through hash linking and sequence numbers
- Message modification/deletion detection via cryptographic chaining
- Pre-rotation protects against future key exposure
- Threshold signatures for multi-party control

### Resolution Without Universal Infrastructure
Unlike other DID methods, KERI can resolve identifiers by:
1. Direct KEL exchange between parties
2. Witness network queries (configurable witnesses)
3. Optional anchoring to any ledger for additional assurance

## Recommendation
**Recommended - Requires Investigation**

KERI represents the most promising approach for a ledger-based registry seeking independence from specific blockchain infrastructure. Key advantages:

1. **True ledger agnosticism**: Can work with any storage backend or none at all
2. **Self-certifying**: Basic verification requires no external lookups
3. **Flexible deployment**: From fully decentralized witness networks to direct peer exchange
4. **Strong security model**: Pre-rotation, threshold signatures, duplicity detection
5. **Active ecosystem**: DIF backing with enterprise adoption

**Investigation areas:**
- KEL storage and synchronization strategies for your specific use case
- Witness configuration requirements and trust model
- Integration complexity with existing systems
- Library maturity for your target platforms

KERI is the only method in this evaluation that can genuinely operate without mandatory external blockchain or HTTP infrastructure dependencies, making it uniquely suitable for self-contained ledger-based registry applications.
