# Plan: Midnight-Native Secure Onboarding Design Document

## Context

The MVE-Planning repo tracks investigations for the Midnight OS user experience driving requirements for Nightstream and Starstream. This document synthesizes research from 8 streams (CAKE, OpenWallet, DeRec, NEAR, zkMe, ENS, TEEs, Midnight platform docs) into a comprehensive, Midnight-native onboarding specification.

Midnight's architecture (BLS12-381/Jubjub curves, Compact circuits, witness-based ZK authorization, three-layer wallet, native attestation trees) fundamentally reshapes the design compared to generic blockchain wallets.

## Approach

### Document Structure

1. **Executive Summary** — QR scan → seed in TEE → three-layer wallet → identity credentials → full access in under 60 seconds (excluding proof generation)

2. **Design Principles**
   - Keys never leave TEEs; authorization via ZK witnesses (Midnight-native)
   - Single BIP39 seed → CIP-1852 HD derivation → Shielded + Night + Dust wallets
   - Chain abstraction (CAKE-inspired intent model)
   - One-key-per-device (NEAR-inspired multi-key accounts)
   - Privacy-by-design identity (zkMe-inspired zkKYC + Midnight attestation trees)
   - Seedless UX: no mnemonics shown to users

3. **System Architecture** — ASCII layered diagram
   - Layer 1: TEE (seed storage, key derivation, signing)
   - Layer 2: Midnight Wallet (Shielded/Night/Dust three-layer model)
   - Layer 3: Account Model (NEAR-inspired multi-key, function-call keys for dApps)
   - Layer 4: Identity (attestation trees + Soulbound Tokens + selective disclosure)
   - Layer 5: Naming (ENS-inspired human-readable identifiers)
   - Layer 6: Chain Abstraction (cross-chain via MPC Chain Signatures)

4. **Human-Readable Naming System** — ENS-inspired, adapted for Midnight
   - **Architecture**: Two-layer contract model adapted to Compact
     - Registry contract: maps namehashes to owners/resolvers/TTL
     - Resolver contracts: map names to addresses (shielded + unshielded + multi-chain)
   - **Namehash**: Recursive hashing algorithm (adapted from ENS EIP-137 to use Midnight's `persistentHash` instead of keccak256)
   - **Name format**: `username.midnight` (or `.mn` short form)
     - Hierarchical: `alice.org.midnight`, `payment.alice.midnight`
     - Subdomain delegation: orgs can issue subnames gaslessly
   - **Registration flow**: Commit-reveal (adapted for Compact circuits)
     - Step 1: `commit(persistentHash(name + secret + owner))` — hides desired name
     - Step 2: Wait ~60 seconds (or N blocks)
     - Step 3: `register(name, owner, duration, secret, resolver, records)` — reveals and registers
     - Prevents frontrunning via ZK commitment
   - **Multi-address resolution** (critical for Midnight's dual-address model):
     - `addr(namehash)` → `UserAddress` (unshielded Night transfers)
     - `shieldedAddr(namehash)` → `ZswapCoinPublicKey` (shielded token operations)
     - `addr(namehash, coinType)` → cross-chain addresses (BTC, ETH, SOL via SLIP-44)
     - Single name resolves to all three Midnight address types + external chains
   - **Privacy-aware resolution** (Midnight advantage over ENS):
     - Shielded address resolution can require ZK proof of authorization
     - Resolution queries via CCIP-Read don't create on-chain traces
     - Selective record visibility: public profile (avatar, bio) vs private records (shielded address)
   - **Offchain subdomains** (CCIP-Read + wildcard pattern):
     - Organizations issue `member.org.midnight` subdomains at zero gas cost
     - Wildcard resolver handles entire subdomain tree from single contract
     - Offchain data stored by org, resolved on demand
     - Enables instant onboarding: new user gets `username.midnight` as part of QR flow
   - **Profile records** (adapted from ENS text records):
     - Avatar (IPFS or on-chain reference)
     - Display name, bio, social links
     - Content hash (decentralized website hosting)
     - Custom application-specific fields
     - zkMe credential status (verified/unverified, without revealing PII)
   - **Name Wrapper with fuses** (permission control for subdomains):
     - `CANNOT_TRANSFER`: Lock name to owner (soulbound names)
     - `CANNOT_CREATE_SUBDOMAIN`: Prevent further hierarchy
     - Custom fuses for application-specific restrictions
   - **Governance**: DAO-controlled registry and registrar parameters
   - **Integration with onboarding**: Name registration is part of the QR onboarding flow
     - User scans QR → seed generated → wallet created → name registered → helpers designated
     - Name becomes the user's universal identifier across all Midnight dApps
   - **Comparison**: ENS (keccak256, Ethereum-only then multi-chain) vs NEAR (protocol-level `.near`) vs Midnight (Compact circuit, privacy-native, multi-address)
   - ASCII diagram: name resolution flow (registry → resolver → address types)

5. **Midnight Platform Integration** — How the design maps to Midnight's architecture (renumbered from 4)
   - **Curves**: BLS12-381 scalar field + Jubjub embedded curve (NOT secp256k1/Ed25519)
     - iOS Secure Enclave P-256 wrapping key encrypts BLS12-381 seed material
     - All on-chain operations use Jubjub/BLS12-381 natively
   - **Wallet model**: HD derivation via BIP32/BIP39/CIP-1852 → three address types:
     - `ZswapCoinPublicKey` (32 bytes) — shielded token operations
     - `UserAddress` (32 bytes) — unshielded Night transfers
     - Dust address — fee token (non-transferable, regenerates from Night)
   - **Address format**: Bech32m with `mn_` prefix
   - **Authorization**: Witness-based ZK proofs (secrets never on-chain)
     - `witness get_key(): Bytes<32>` provides secret key
     - Circuit proves `hash(witness_key) == ledger_owner` without revealing key
     - Perfect alignment with TEE model: key derived in TEE, used as witness, never exported
   - **Token operations**: All transfers go through Compact circuits (~18-21s proof generation)
     - Shielded: mintShieldedToken, sendShielded, receiveShielded (ZK-private)
     - Unshielded: mintUnshieldedToken, sendUnshielded (transparent, BIP-340 Schnorr signed)
   - **Fees**: DUST regenerates from NIGHT holdings; developers can subsidize new users
   - **Private state**: AES-256-GCM + PBKDF2 encrypted local storage per contract
   - **Proof pipeline**: dApp → SDK → local execution → Proof Server (~18s) → Node verification

5. **QR Code Onboarding Flow** — ASCII sequence diagram
   - Phase 1: QR scan (server-authenticated ECDH, signed payload, pinned server key)
   - Phase 2: Seed generation inside TEE (256-bit entropy → BIP39 → CIP-1852 derivation)
   - Phase 3: Three wallet addresses derived (Shielded, Night, Dust)
   - Phase 4: Account registration (named account with first device key)
   - Phase 5: Developer-subsidized NIGHT airdrop (enables DUST generation for fees)
   - Phase 6: Social account linking (OAuth2 for recovery factors)
   - Phase 7: DeRec helper designation
   - Second-device linking via QR (adds independent key to same account)

6. **Account Model** — NEAR-inspired, adapted for Midnight
   - Named accounts decoupled from any single key
   - Full-access keys (per device, TEE-held) — can perform any operation
   - Function-call keys (per dApp) — scoped to specific Compact circuits with DUST allowances
   - Device addition: new device generates own seed in TEE, adds key to account via circuit
   - Device revocation: any remaining full-access key can remove compromised device key
   - No account change on key rotation (account ID independent of keys)
   - ASCII diagram: multi-device account model

7. **Identity and Credential Layer** — Midnight-native attestation trees + zkMe patterns
   - **Midnight's native credential model** (from developer guide):
     - Issuer verifies user attributes off-chain
     - Creates Merkle tree leaves: `persistentHash([domain_sep, user_secret_key])`
     - Publishes Merkle root to Compact contract
     - User holds secret key + Merkle proof (sibling hashes)
     - Circuit proves membership without revealing identity
     - Nullifier (different domain sep) prevents credential reuse
     - Domain separation ensures unlinkability across services
   - **Multi-attribute attestation trees**:
     - Age >= 18: domain "age18:", separate Merkle root
     - Citizenship: domain "eures:", separate Merkle root
     - Accredited investor: domain "acred:", separate Merkle root
     - Single circuit proves multiple properties simultaneously
   - **zkMe integration**: zkKYC as the off-chain issuer layer
     - zkMe performs identity verification (OCR + liveness + FHE biometrics)
     - Generates Groth16 zk-SNARK proofs of identity claims
     - Mints Soulbound Tokens on Midnight (non-transferable credential anchors)
     - Builds attestation trees with user's Midnight secret key as leaf input
     - User receives Merkle proofs for each verified attribute
   - **Privacy guarantees**:
     - Witness values (secret key, Merkle proofs) never leave device
     - Compact's disclosure analysis enforces privacy at compile time
     - Service sees only nullifier + boolean eligibility result
     - No PII on-chain at any point
   - ASCII diagram: attestation tree structure and verification flow

8. **Key Management** — Single seed → Midnight's three-layer wallet
   - BIP39 mnemonic (hidden from user) → BIP32 master key → CIP-1852 account derivation
   - Role-based derivation: External (addresses), Change (internal), Staking, Dust
   - TEE wrapping: Secure Enclave AES-256-GCM key wraps seed at rest
   - Key derivation happens in app memory (decrypted from TEE, zeroized after use)
   - Chain-level caching: derive and cache encrypted Shielded/Night/Dust keys separately
   - Signing: BIP-340 Schnorr for unshielded UTXOs, ZK witness for shielded operations
   - ASCII diagram: CIP-1852 derivation tree for Midnight

9. **Recovery Design** — DeRec + Midnight wallet recovery
   - **DeRec for seed recovery**: Shamir SS with Merkle commitments
     - Split raw BIP39 seed via (3,5) threshold
     - Helpers receive encrypted shares (ML-KEM post-quantum transport)
     - Daily challenge-response verification
     - Epoch-based resharing (suspend verifications during transitions)
   - **Midnight wallet recovery flow**:
     - Recover seed from DeRec helpers
     - Re-derive all three wallet layers (Shielded, Night, Dust) from seed
     - Re-derive secret keys for attestation tree membership
     - Identity credentials survive: SBTs persist on-chain, Merkle roots unchanged
     - Nullifiers already used are tracked on-chain (no double-claim risk)
     - Private state must be re-imported (exported backup or re-synced)
   - **Multi-key account recovery** (NEAR-inspired):
     - If one device lost: use other device's key to revoke and re-add
     - If all devices lost: DeRec seed recovery → generate new device keys → re-register
   - ASCII diagram: recovery flow

10. **Security Model** — Threat model with Midnight-specific mitigations
    - **CRITICAL: Seed exposure in app memory** — mitigated by chain-level key caching, minimal decryption window, mlock/zeroization
    - **HIGH: iOS P-256 ↔ BLS12-381 incompatibility** — use Secure Enclave AES-256-GCM symmetric key (not P-256 asymmetric) to wrap BLS12-381 seed; Midnight uses Jubjub/BLS12-381 natively, not secp256k1
    - **HIGH: QR protocol** — server-authenticated ECDH, visual confirmation codes, single-use sessions, 60-second timestamps
    - **HIGH: Single seed risk** — DeRec (3,5) threshold, BIP-85 compartmentalization option
    - **MEDIUM: Proof generation latency** (~18-21s) — not a security issue but affects UX; show progress indicator
    - **MEDIUM: Quantum resistance** — BLS12-381 signing keys vulnerable to Shor; DeRec uses ML-KEM; monitor NIST PQ signature standards
    - **INFO: Midnight compiler-enforced privacy** — disclosure analysis catches witness leaks at compile time
    - Trust boundary ASCII diagram

11. **Cross-Platform Considerations**
    - iOS Secure Enclave: P-256 only → AES-256-GCM wrapping key for BLS12-381 seed
    - Android StrongBox: Broader curve support but slow (~9s key gen) → same AES wrapping approach for consistency
    - Laptop: ARM CCA / TPM for TEE
    - All platforms: same BIP39 seed → same CIP-1852 derivation → same addresses
    - Cross-platform recovery always via DeRec (TEE-wrapped blobs are non-portable)

12. **Appendix A: Security Review Summary** — 10 cryptographer findings + 23 protocol analysis findings with severity ratings and Midnight-specific mitigations

13. **Appendix B: Midnight Platform Quick Reference** — Compact types, kernel operations, circuit compilation pipeline, SDK transaction lifecycle

14. **Appendix C: Research Sources** — CAKE, OpenWallet, DeRec, NEAR, zkMe, ENS, Midnight docs

### Key Design Decisions

- **Curves**: Midnight uses BLS12-381/Jubjub natively — no secp256k1/Ed25519 needed, simplifying the TEE story (only need AES wrapping, not curve translation)
- **HD derivation**: CIP-1852 (Cardano-style) via BIP32/BIP39, generating three wallet address types from single seed
- **Authorization**: ZK witness-based (Midnight-native) — secret key stays in TEE, used as Compact witness, circuit proves knowledge without revealing it
- **Identity**: Midnight attestation trees (Merkle roots on-chain, leaves off-chain) + zkMe as off-chain issuer for zkKYC
- **Fees**: Developer-subsidized DUST airdrop at onboarding (DUST regenerates from NIGHT, non-transferable)
- **Account model**: NEAR-inspired multi-key (each device = independent key, account ID stable)
- **Recovery**: DeRec (3,5) threshold for seed + NEAR-style key rotation for account-level recovery
- **QR auth**: Server-signed ECDH, pinned key, visual confirmation, single-use atomic sessions
- **Naming**: ENS-inspired `username.midnight` with Compact circuit registry, multi-address resolution (shielded + unshielded + cross-chain), privacy-aware resolution, gasless subdomains via wildcard + CCIP-Read

### ASCII Diagrams (11)

1. System architecture (6-layer model including naming)
2. QR onboarding sequence (including name registration step)
3. Second-device linking
4. CIP-1852 key derivation tree for Midnight
5. TEE wrapping architecture (AES-256-GCM boundary)
6. Name resolution flow (registry → resolver → shielded/unshielded/cross-chain addresses)
7. Attestation tree structure (Midnight credential model)
8. zkKYC verification flow (zkMe → attestation tree → Compact circuit)
9. DeRec recovery flow
10. Multi-key account model
11. Trust boundary diagram (TEE / app memory / proof server / chain)

### Critical Files

- **Create**: [Onboarding design](./machine-investigation/key-flows/secure-onboarding-design.md)
- **Update**: [Key flows](./flows.md)
- **Reference**: [Midnight reference](`../machine-investigation/midnight-v1-documentation`)
- **Reference**: [zkME](../references/zkme.pdf) (zkKYC whitepaper)

## PM Artifacts

Three product management artifacts ground the technical design in user-centered thinking:

- **Problem Framing Canvas** (`machine-investigation/personas/problem-framing-canvas.md`) — MITRE canvas identifying biases (QR accessibility, self-custody assumption, smartphone/ID requirement), left-out populations, and a "How Might We" question targeting <60s, <3 decisions onboarding
- **Proto-Personas** (`machine-investigation/personas/proto-personas.md`) — Newcomer Nadia (Nairobi, M-Pesa native), Developer Dev (Berlin, DeFi builder), Enterprise Ethan (Frankfurt, MiCA compliance)
- **Problem Statements** (`machine-investigation/personas/problem-statements.md`) — Per-persona framing narratives with barrier-to-design-section mapping table; identifies 3 critical gaps: SDK onboarding wrapper, lawful access mechanism, credential lifecycle management

### Known Gaps (from PM analysis)
- Design has no alternative onboarding entry point for users who can't scan QR codes (NFC, deep link)
- No `onboardUser()` SDK wrapper for developers (protocol specified, API not shipped)
- No institutional helper role in DeRec (enterprise recovery path)
- No SBT expiration/revocation workflow

## Verification

- Document uses Midnight-specific terminology (Compact, ZKIR, Zswap, DUST, NIGHT)
- All cryptographic primitives match Midnight's actual stack (BLS12-381, Jubjub, Poseidon, BIP-340)
- Attestation tree design uses actual Compact patterns from developer guide
- Wallet model reflects actual three-layer architecture (Shielded/Night/Dust)
- ASCII diagrams render correctly
- README.md updated with link to new doc
- PM artifacts verified against design doc (30/31 checks pass, gaps documented)
