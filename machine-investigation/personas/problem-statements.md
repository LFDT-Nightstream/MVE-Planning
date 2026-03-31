# Problem Statements: Midnight Secure Onboarding

> **Date:** 2026-03-29
> **Product:** Midnight-Native Secure Onboarding
> **Source:** Design doc v0.2 (`secure-onboarding-design.md`), persona research
> **Version:** v0.2

---

## Persona 1: Newcomer Nadia

**Age:** 28 | **Role:** Marketing coordinator | **Location:** Nairobi, Kenya | **Device:** iPhone 12

### Problem Framing Narrative

**I am:**
- A professional in Nairobi who sends money home to my parents in Kisumu every month through M-Pesa -- I tap, confirm, done
- Someone who has heard colleagues talk about crypto and DeFi but has never held a token, opened a wallet, or understood why I would want to
- A person who tried to set up a crypto wallet once at a friend's urging, got to the part where it told me to write down 12 words on paper and keep them safe forever, and closed the app
- Comfortable with mobile payments but deeply skeptical of anything that feels like it was designed for programmers, not people

**Trying to:**
- Send and receive digital value as simply as I do with M-Pesa -- scan something, confirm, move on with my life
- Explore what blockchain can do for me (cheaper remittances, access to savings tools my bank does not offer) without feeling like I need a computer science degree first
- Own my own account in a way I actually understand and can recover if my phone is stolen on the matatu

**But:**
- Every crypto wallet I have seen demands I write down a seed phrase -- 12 or 24 random words that are apparently the only way to get my money back if something goes wrong, and if I lose them, everything is gone forever
- The addresses I am supposed to send money to look like `mn_shield-addr1qx7z...` -- a 60-character string of nonsense that I cannot read, cannot remember, and cannot verify I copied correctly
- I do not understand why I would need three different kinds of tokens (Shielded, NIGHT, DUST) just to use one platform -- M-Pesa has one balance, one currency, one screen
- Every dApp I try to use asks me to "connect my wallet" and approve permissions I can't parse -- shielded scopes, proof authorization, credential disclosure. It's like being asked to read a legal contract every time I open an app.
- Before I can do anything, I apparently need to buy a "gas token" to pay for transactions, but nobody has explained how to buy this token without already having the token

**Because:**
- Blockchain systems have historically been designed by cryptographers for cryptographers. The underlying architecture of Midnight -- BLS12-381 curves, Poseidon hash functions, three-layer wallet derivation, ZK witness authorization -- is genuinely sophisticated, but that sophistication has been allowed to leak directly into the user experience. Nobody has built the M-Pesa-level abstraction layer that hides the plumbing and shows me only the faucet. The assumption baked into most wallets is that users will tolerate managing raw cryptographic material, and that assumption filters out everyone who is not already a crypto native.

**Which makes me feel:**
- Honestly? Stupid. And I know I am not stupid -- I manage campaign budgets and vendor contracts across three countries. But when I open a crypto app and it asks me to "securely store your BIP39 mnemonic" and shows me an address that looks like someone sat on a keyboard, I feel like this was not made for me. I feel excluded. And then I feel annoyed, because the promise of crypto is supposedly financial inclusion, but the first thing it does is exclude anyone who does not already speak the language. I close the app and go back to M-Pesa, which never made me feel dumb for wanting to send money.

### Context & Constraints

- **Geographic:** Kenya -- mobile-first economy where M-Pesa processes more transactions than all commercial banks combined. Nadia's mental model for "sending money" is tap-and-confirm, not address-and-broadcast.
- **Technical:** iPhone user, strong mobile literacy, but no exposure to blockchain concepts. Her phone supports Secure Enclave (TEE), but she does not know or care what that means.
- **Economic:** Cannot afford to lose money to a mistake. No tolerance for "learning experiences" that cost real value. Needs subsidized onboarding -- she will not buy a gas token before she understands why the platform is worth using.
- **Cultural:** High trust in mobile money systems (M-Pesa, Airtel Money). Low trust in anything that looks like a scam or a technical experiment. Will not persist through friction -- if it does not work in the first 60 seconds, she is gone.
- **Connectivity:** Reliable 4G in Nairobi but intermittent when traveling. Onboarding flow must tolerate brief network drops without forcing a restart.

### Final Problem Statement

Nadia, a mobile-savvy professional in Nairobi, needs a way to start using blockchain-based financial tools as effortlessly as she uses M-Pesa because current wallets demand she manage cryptographic secrets, parse incomprehensible addresses, and pre-purchase fee tokens -- which makes her feel excluded from a technology that was supposed to include her.

---

## Persona 2: Developer Dev

**Age:** 34 | **Role:** Senior TypeScript engineer | **Location:** Berlin, Germany | **Specialty:** Building DeFi protocols on Midnight

### Problem Framing Narrative

**I am:**
- A TypeScript/React developer building a DeFi lending protocol on Midnight, responsible for the end-to-end user experience from first visit to first transaction
- Someone who deeply understands Midnight's Compact language, ZK proof pipeline, and three-layer wallet model -- and who also understands that my users do not and never will
- An engineer who has watched promising DeFi projects die not because the smart contracts were bad, but because user onboarding had a 90% drop-off rate at the "install wallet and configure it" step
- A builder whose own product's success depends entirely on whether non-technical users can get through the door

**Trying to:**
- Embed Midnight wallet creation and identity verification directly into my dApp's onboarding flow so users never leave my application, never install a separate wallet, and never encounter raw blockchain concepts
- Offer my users a "scan a QR code and you are in" experience that provisions a named account (`username.midnight`), funds it with enough NIGHT for initial DUST generation, and issues the privacy-preserving credentials my protocol requires -- all through a single SDK call
- Decouple my dApp's authentication model from individual device keys so that when a user switches phones, they do not lose access to their lending positions

**But:**
- Midnight's wallet SDK is a 13-package surface area where I have to manually orchestrate seed generation, TEE wrapping, CIP-1852 derivation across five role paths, commit-reveal name registration, DUST regeneration tracking, and attestation tree enrollment -- each with its own failure modes and timing constraints
- The proof generation pipeline requires a local Rust process (~18-21 seconds per circuit call), and I have no clean way to manage user expectations during these waits or to parallelize proof generation with other onboarding steps through the SDK
- The `midnight.onboard()` API exists (Section 5.10) but it's a spec, not shipped code. I need a battle-tested npm package with TypeScript types, error boundaries, retry logic, and proof-generation progress hooks.
- Function-call keys are specified (Section 7.2) and the MCP handles session scopes (Section 5.9), but I still need a React hook library (`useMidnightConnect`) that wraps the protocol spec into 3-line integration.

**Because:**
- Midnight's architecture is powerful and the developer abstractions are now specified -- `midnight.onboard()` (Section 5.10) defines three integration models (full, modular, headless), the Midnight Connection Protocol (Section 5.9) standardizes dApp-wallet communication, and function-call keys (Section 7.2) are fully designed. But the gap between spec and npm package is where my users bounce. A protocol specification in a design doc is not a `npm install @midnight/onboard` with TypeScript types, error boundaries, and a Storybook of pre-built components. Every DeFi developer on Midnight is reading the same spec and each implementing their own interpretation of it, with their own bugs and their own edge-case handling.

**Which makes me feel:**
- Torn. I genuinely love building on Midnight -- the privacy guarantees are real, the Compact compiler catches disclosure bugs at compile time, and the attestation tree pattern is elegant. But every sprint, I spend more time wiring up wallet plumbing than building my actual product. I feel like I am writing the same onboarding code that every other Midnight developer is writing, and none of us are doing it as well as the platform team could if they shipped it as a first-party SDK. I am frustrated because the hard problems (ZK proofs, privacy-preserving identity, seedless recovery) are already solved in the design -- but the last mile, making it easy for *me* to make it easy for *my users*, is still missing. And I worry: if I ship with a clunky onboarding, users will blame my product, not the platform.

### Context & Constraints

- **Technical:** TypeScript/React stack. Needs a JavaScript/TypeScript SDK that wraps the Rust proof server and TEE interactions. Cannot require users to install native binaries or browser extensions.
- **Platform:** Building on Midnight -- must work within its BLS12-381 curve constraints, Compact circuit model, and three-layer wallet architecture. No shortcuts that bypass ZK proofs.
- **Timing:** Proof generation at 18-21 seconds per circuit call is a hard constraint. The SDK must help him overlap proof generation with user-facing steps (choosing a username, linking social accounts) rather than serializing everything.
- **Regulatory:** His DeFi protocol will serve EU users and must integrate zkKYC for MiCA compliance. He needs credential verification baked into onboarding, not bolted on after the fact.
- **Economics:** Must subsidize new user onboarding by airdropping NIGHT from a developer-funded wallet. Needs clear APIs for calculating airdrop amounts, tracking DUST generation rates, and managing the developer subsidy budget.
- **Competitive:** Other DeFi protocols on other chains offer one-click wallet creation via account abstraction. If Midnight onboarding takes 5 minutes of manual configuration, users will go elsewhere.

### Final Problem Statement

Dev, a DeFi developer building on Midnight, needs a composable SDK that wraps the seven-phase onboarding flow into a single integration point because today he must re-implement wallet orchestration, proof timing, fee subsidization, and credential issuance from raw primitives -- which delays his product, fragments the ecosystem, and makes every Midnight dApp's onboarding only as good as each individual developer's plumbing skills.

---

## Persona 3: Enterprise Ethan

**Age:** 45 | **Role:** Compliance officer | **Location:** Frankfurt, Germany | **Organization:** MiCA-licensed European fintech offering tokenized securities

### Problem Framing Narrative

**I am:**
- The head of compliance at a MiCA-licensed fintech that is moving its tokenized securities issuance to Midnight for the privacy-preserving properties our institutional clients demand
- A person who has spent two decades navigating financial regulation and understands that "privacy" and "compliance" are not opposites -- but who also knows that regulators will shut us down in a heartbeat if we cannot demonstrate both
- Someone who must sign off on every user-facing flow before it goes live, and who needs to explain to the BaFin examiner exactly how our KYC process works, what data we retain, and how we can produce an audit trail -- even when the underlying system uses zero-knowledge proofs
- An operator who has seen compliance "solutions" that are really just checkboxes that would not survive a real regulatory examination

**Trying to:**
- Onboard institutional and retail clients onto a Midnight-based platform where their identity is verified to MiCA standards (KYC/AML, travel rule, sanctions screening) but their personal data is never stored on-chain or in any system I would have to disclose in a breach notification
- Produce an audit trail that satisfies BaFin, ESMA, and our external auditors -- proving that every user passed KYC, that credentials have not expired, and that sanctioned individuals are excluded -- without compromising the zero-knowledge properties that make Midnight attractive to our clients in the first place
- Implement account recovery procedures that meet our operational resilience requirements (DORA) while keeping key material out of our custody -- because the moment we hold users' keys, we become a custodian under MiCA and trigger an entirely different (and far more expensive) licensing regime

**But:**
- The zkKYC model uses attestation trees where the Merkle root is on-chain but the leaves (the actual credential proofs) are held locally on user devices -- which means I cannot independently verify a user's credential status without their cooperation, and I have no way to produce a regulatory report that says "here are all verified users as of this date"
- Nullifiers prevent double-claiming but are unlinkable to Merkle leaves by design -- which is a privacy feature, but it means I cannot map a suspicious transaction back to a verified identity without the user's participation, even under a lawful court order
- Institutional recovery is specified (Section 10.5) with TEE-based helpers, multi-factor release policies, and audit trails. But no vendor operates this service. I can't present a GitHub spec to my auditors -- I need a contracted service with SLAs.
- Credential lifecycle management exists (Section 8.7) with root-versioned expiration and revocation. But there's no compliance dashboard, no batch query API for my screening vendor, and no integration with our existing FATF Travel Rule workflow.

**Because:**
- Midnight's privacy architecture was designed for a world where the user is sovereign and no third party should be able to surveil, revoke, or override their credentials unilaterally. That is philosophically sound and technically elegant. The design has evolved to acknowledge regulated use cases: institutional recovery (Section 10.5) defines TEE-based helpers with multi-factor release policies and audit trails, and credential lifecycle management (Section 8.7) specifies root-versioned expiration and revocation mechanisms. These mechanisms are specified but the vendor and tooling ecosystem hasn't formed around them yet. No recovery service provider operates the institutional helper model. No compliance dashboard vendor has built on top of the credential lifecycle spec. No screening vendor has integrated the batch query APIs. The gap is no longer "no mechanism exists" -- it is that the mechanisms are designed but the commercial ecosystem of service providers, SLA-backed vendors, and compliance tool integrators that a regulated entity needs to actually operationalize them does not yet exist.

**Which makes me feel:**
- Exposed. I am genuinely excited about what Midnight offers -- our institutional clients are tired of putting their transaction data on public ledgers for competitors to analyze, and the shielded token model solves a real problem for them. But I cannot recommend we go live until I can look our BaFin examiner in the eye and explain the audit trail. Right now, I would be asking them to trust a system where I cannot independently verify user credentials, cannot revoke compromised identities without issuer cooperation, and cannot guarantee account recovery for estates or incapacitated users. That is not a technology problem -- it is a "we lose our license" problem. I feel caught between the future I want to build toward and the regulatory reality I have to operate in today. And I feel alone in this, because most of the blockchain conversation treats compliance as an afterthought or an enemy, when for me it is the prerequisite for everything else.

### Context & Constraints

- **Regulatory:** MiCA (Markets in Crypto-Assets Regulation) requires full KYC/AML for all users, transaction monitoring, travel rule compliance for transfers above 1,000 EUR, and operational resilience under DORA. BaFin is the supervising authority. Penalties for non-compliance include license revocation.
- **Legal:** Must support lawful access requests (court orders for identity disclosure on specific transactions). Must support estate and incapacitation recovery scenarios with documented procedures that a probate court would accept.
- **Audit:** External auditors (Big Four) require periodic reports on: total verified users, credential expiration status, sanctions screening coverage, and incident response documentation. These reports must be producible without depending on user cooperation.
- **Technical:** Needs issuer-side tooling -- credential lifecycle dashboards, batch Merkle root updates for revocation, SBT expiration mechanisms, and aggregate compliance reporting APIs. These must work within Midnight's privacy model, not around it.
- **Organizational:** Compliance team is non-technical. Tooling must present audit information through a web dashboard, not through chain queries or Compact circuit invocations.
- **Competitive:** Other tokenized securities platforms (on Polygon, Avalanche subnets) offer built-in compliance tooling. If Midnight cannot match this, the privacy advantages are moot -- the fintech simply cannot adopt the platform.

### Final Problem Statement

Ethan, a compliance officer at a MiCA-licensed fintech, needs issuer-side tooling that provides auditable credential lifecycle management, regulatory reporting, and legal recovery pathways on top of Midnight's zero-knowledge architecture because without them, the very privacy properties that attract his institutional clients also prevent him from meeting the regulatory obligations that allow his business to exist.

---

## How the Design Addresses These Problems

The mapping below connects each persona's specific barriers to the design sections that address them. Where gaps remain, they are noted.

### Newcomer Nadia

| Barrier | Design Section | Solution | Status |
|---|---|---|---|
| Seed phrase complexity -- will bail if asked to write down 12/24 words | Section 2.6 (Seedless UX), Section 6 Phase 2 | BIP39 seed generated inside TEE, encrypted immediately with AES-256-GCM wrapping key, never displayed to user. Recovery via DeRec helpers, not mnemonic backup. | ADDRESSED -- this is a core design principle. |
| Incomprehensible addresses (`mn_shield-addr1qx7z...`) | Section 2.3 (Chain Abstraction), Section 4 (Naming System), Section 6 Phase 4 | Human-readable `username.midnight` names resolve to all three address types automatically. Multi-address resolver selects the correct address based on operation context. Users see names, never raw addresses. | ADDRESSED. |
| Three token types are confusing (Shielded, NIGHT, DUST) | Section 2.3 (Chain Abstraction via Intent Model) | Intent-based interface: user says "send 100 to alice.midnight" and the system routes to the correct token type and address. Chain abstraction hides wallet-layer complexity. | PARTIALLY ADDRESSED -- the intent model is designed but UX copy and progressive disclosure for Nadia's level of understanding need product design work beyond the architecture doc. |
| dApp connection UX -- asked to approve scopes, proof authorization, and credential disclosure I cannot parse | Section 5.9 (Midnight Connection Protocol with progressive authorization) | MCP defines a standardized connection flow with progressive authorization: dApps request scopes incrementally, and the wallet presents permissions in human-readable form. Users approve only what they need, when they need it. | PARTIALLY ADDRESSED -- MCP is specified but adoption depends on dApp developers implementing the standard rather than building custom connectors. The human-readable permission language needs UX testing with non-technical users like Nadia. |
| Must buy gas tokens before first transaction | Section 6 Phase 5 (Developer-Subsidized Airdrop), Section 5.3 (Fee Economics) | Developer/onboarding provider airdrops NIGHT during onboarding, which auto-generates DUST for fee payment. User never purchases gas tokens. First transactions are fully subsidized. | ADDRESSED -- explicitly addressed in the seven-phase flow. |
| Phone theft/loss means losing everything | Section 2.4 (One Key Per Device), Section 10 (Recovery Design) | Multi-device key model means losing one device does not lose the account. Full loss triggers DeRec recovery: contact 3 of 5 helpers to reconstruct seed on new device. Social account linking (Phase 6) provides secondary recovery factor. | PARTIALLY ADDRESSED -- Nadia may not have 5 trusted contacts with the Midnight app installed. Helper availability in early adoption markets is a bootstrapping challenge. |

### Developer Dev

| Barrier | Design Section | Solution | Status |
|---|---|---|---|
| 13-package SDK surface area requiring manual orchestration of seed, TEE, derivation, naming, DUST, and credentials | Section 3.3 (Data Flow During Onboarding), Section 5.10 (SDK Onboarding Wrapper), Section 6 (QR Onboarding Flow) | The seven-phase onboarding protocol is fully specified. Section 5.10 defines `midnight.onboard()` with three integration models (full, modular, headless) that wrap the orchestration into a single API surface. | PARTIALLY ADDRESSED (specified, not shipped) -- the `midnight.onboard()` API is designed with three models and clear TypeScript signatures, but it is a spec, not a published npm package. Dev needs shipped code with error boundaries, retry logic, and proof-generation progress hooks. |
| Proof generation latency (18-21 seconds per circuit) with no clean SDK support for parallelization or UX management | Section 5.4 (ZK Proof Generation Performance), Section 5.10 (SDK Onboarding Wrapper), Section 6.1 (Protocol Overview timing table) | Design explicitly overlaps proof generation with off-chain operations. The SDK wrapper spec (Section 5.10) includes progress callback hooks and parallelization strategy. | PARTIALLY ADDRESSED (specified, not shipped) -- the parallelization strategy and progress callback API are designed in the spec, but SDK-level primitives for managing concurrent proof generation and user-facing state machines need to be implemented and tested. |
| `midnight.onboard()` API exists but is a spec, not shipped code -- needs TypeScript types, error boundaries, retry logic, progress hooks | Section 5.10 (SDK Onboarding Wrapper), Section 6.3 (Phase Details), Section 6.1 (atomicity guarantee) | `midnight.onboard()` defines three integration models with atomic rollback semantics, artifact-passing between phases, and server-side subsidization. | PARTIALLY ADDRESSED (specified, not shipped) -- the API is fully designed but not yet a published, battle-tested npm package. This is the highest-impact remaining gap for Dev. |
| Function-call keys and MCP session scopes are specified but need a React hook library for 3-line integration | Section 2.4 (One Key Per Device), Section 5.9 (Midnight Connection Protocol), Section 7.2 (Key Types) | NEAR-inspired function-call keys with circuit-specific scoping and DUST spending limits. MCP (Section 5.9) standardizes dApp-wallet connection with progressive authorization and session scopes. | PARTIALLY ADDRESSED (specified, not shipped) -- function-call keys and MCP are fully designed, but a developer-friendly React hook library (`useMidnightConnect`) that wraps the protocol spec into simple integration does not yet exist. |
| dApp-wallet connection requires understanding CAIP scopes and building custom connectors | Section 5.9 (Midnight Connection Protocol) | MCP defines a standardized connection protocol with progressive authorization, session management, and human-readable scope descriptions. dApps request permissions incrementally rather than requiring blanket authorization. | PARTIALLY ADDRESSED (specified, not shipped) -- MCP is specified but adoption depends on ecosystem tooling (React hooks, reference implementations) and dApp developer willingness to adopt the standard. |

### Enterprise Ethan

| Barrier | Design Section | Solution | Status |
|---|---|---|---|
| Cannot independently verify user credential status without user cooperation (leaves held on-device) | Section 8.1-8.2 (Attestation Trees), Section 8.3 (zkMe as Issuer), Section 8.7 (Credential Lifecycle Management) | zkMe as trusted issuer maintains the full Merkle tree and can report aggregate verification statistics. Soulbound Tokens provide on-chain credential status anchors visible to any observer. Section 8.7 adds root-versioned expiration and revocation mechanisms. | PARTIALLY ADDRESSED -- zkMe holds the tree and Section 8.7 adds lifecycle management, but issuer-side reporting APIs, compliance dashboards, and batch query tools for "show me all verified users" are not yet built as products. Ethan needs an issuer portal, not just a Compact contract and a spec. |
| Nullifiers are unlinkable to identities by design -- cannot map suspicious transactions to verified users under court order | Section 8.4 (Nullifier-Based Reuse Prevention), Section 2.5 (Privacy-by-Design) | Domain-separated nullifiers prevent casual linkage. This is a deliberate privacy guarantee. | REMAINING GAP -- the design does not specify a lawful access mechanism (e.g., a compliance-keyed escrow where identity can be revealed under court order with multi-party authorization). This is a fundamental tension between privacy-by-design and regulatory obligations that the design acknowledges but does not resolve. |
| Institutional recovery specified but no vendor operates the service -- cannot present a spec to auditors | Section 10 (Recovery Design), Section 10.5 (Institutional Recovery), Section 2.6 (Seedless UX) | Section 10.5 defines TEE-based institutional helpers with multi-factor release policies (M-of-N quorum, compliance officer sign-off, time-delayed release), full audit trails, and integration with estate/incapacitation legal frameworks. DeRec (3,5) threshold with daily challenge-response provides the underlying mechanism. | PARTIALLY ADDRESSED (specified, vendor ecosystem needed) -- the institutional helper model is fully designed with TEE isolation, multi-factor release, and audit logging. But no vendor operates this service. Ethan needs a contracted service provider with SLAs, not a specification. |
| Credential lifecycle management exists but no compliance dashboard, batch query API, or Travel Rule integration | Section 8.7 (Credential Lifecycle Management), Section 8.3 (SBT Minting), Section 8.2 (Multi-Attribute Attestation Trees) | Section 8.7 specifies root-versioned expiration and revocation: attestation tree roots carry version timestamps, credentials carry expiration metadata, and issuers can publish revocation updates that invalidate credentials against stale roots. SBTs carry lifecycle metadata. | PARTIALLY ADDRESSED (specified, vendor ecosystem needed) -- the credential lifecycle mechanisms (expiration, revocation, root versioning) are designed. But the compliance dashboard, batch query APIs for screening vendors, and FATF Travel Rule workflow integrations that Ethan needs to operationalize them do not yet exist. |

---

### Cross-Persona Design Coverage Summary

The table below provides a consolidated view of how each design doc v0.2 section maps to persona barriers, with a standardized status assessment.

| Design Section | Capability | Persona(s) Affected | Status | What Remains |
|---|---|---|---|---|
| Section 2.3 (Chain Abstraction) | Intent model hides token types and address complexity | Nadia | ADDRESSED | UX copy and progressive disclosure need product design work |
| Section 2.4 (One Key Per Device) | Multi-device key model, function-call keys | Nadia, Dev | ADDRESSED | None at the architecture level |
| Section 2.5 (Privacy-by-Design) | Unlinkable nullifiers, shielded transactions | Ethan | REMAINING GAP | No lawful access mechanism for court-ordered identity disclosure |
| Section 2.6 (Seedless UX) | TEE-wrapped seed, no mnemonic exposure | Nadia | ADDRESSED | None -- core design principle |
| Section 4 (Naming System) | Human-readable `username.midnight` names | Nadia | ADDRESSED | None |
| Section 5.3 (Fee Economics) | Developer-subsidized airdrop, DUST auto-generation | Nadia, Dev | ADDRESSED | None |
| Section 5.4 (ZK Proof Performance) | Proof parallelization strategy | Dev | PARTIALLY ADDRESSED | SDK-level primitives for progress callbacks and concurrent proof management need implementation |
| Section 5.9 (Midnight Connection Protocol) | Standardized dApp-wallet connection with progressive authorization and session scopes | Nadia, Dev | PARTIALLY ADDRESSED (specified, not shipped) | Needs React hook library, reference implementations, and dApp developer adoption. Human-readable permission language needs UX testing. |
| Section 5.10 (SDK Onboarding Wrapper) | `midnight.onboard()` API with three integration models (full, modular, headless) | Dev | PARTIALLY ADDRESSED (specified, not shipped) | Needs published npm package with TypeScript types, error boundaries, retry logic, progress hooks. Highest-impact gap for Dev. |
| Section 6 (QR Onboarding Flow) | Seven-phase onboarding protocol with atomic rollback | Nadia, Dev | ADDRESSED | Protocol is fully specified; SDK wrapper (Section 5.10) bridges it to developers |
| Section 7.2 (Key Types) | Function-call keys with circuit-specific scoping and DUST limits | Dev | PARTIALLY ADDRESSED (specified, not shipped) | Needs developer-friendly React hook (`useMidnightConnect`) wrapping the protocol spec |
| Section 8.1-8.3 (Attestation Trees, zkMe) | zkKYC credential issuance, Merkle tree management, SBTs | Ethan | PARTIALLY ADDRESSED | Issuer-side reporting APIs and compliance dashboards not yet built as products |
| Section 8.4 (Nullifiers) | Domain-separated nullifiers preventing double-claiming | Ethan | REMAINING GAP | Unlinkability by design conflicts with lawful access requirements -- no resolution specified |
| Section 8.7 (Credential Lifecycle Management) | Root-versioned expiration, revocation, credential metadata | Ethan | PARTIALLY ADDRESSED (specified, vendor ecosystem needed) | No compliance dashboard, no batch query API, no FATF Travel Rule workflow integration |
| Section 10 (Recovery Design) | DeRec (3,5) threshold, daily challenge-response, 90-day resharing | Nadia | PARTIALLY ADDRESSED | Helper availability in early adoption markets is a bootstrapping challenge |
| Section 10.5 (Institutional Recovery) | TEE-based institutional helpers, multi-factor release policies, audit trails | Ethan | PARTIALLY ADDRESSED (specified, vendor ecosystem needed) | No vendor operates this service. Ethan needs contracted providers with SLAs, not a spec. |

**Status Legend:**
- **ADDRESSED** -- Design fully resolves the barrier at the architecture level. Implementation work remains standard.
- **PARTIALLY ADDRESSED** -- Mechanism is designed but either unshipped (Dev) or lacking the vendor/tooling ecosystem (Ethan) needed for production use.
- **REMAINING GAP** -- Design does not resolve the barrier, either by deliberate architectural choice (nullifier unlinkability) or because the solution requires work beyond what the design doc can specify.
