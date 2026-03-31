# Proto-Personas: Midnight Secure Onboarding

> **Status:** Draft v0.2 — Assumption-based, not yet validated through user research
> **Date:** 2026-03-29
> **Product:** Midnight-native secure onboarding system
> **Method:** Proto-persona (hypothesis-driven, per Gothelf *Lean UX* framework)

These three personas represent the primary user archetypes for the Midnight onboarding flow. They span the spectrum from end-consumer to developer to enterprise buyer. Each persona should be treated as a testable hypothesis — tag `[ASSUMPTION—VALIDATE]` marks claims that need interview data.

---

## Persona 1: Newcomer Nadia

### Name
- Newcomer Nadia

### Bio & Demographics
- 28 years old, lives in Nairobi, Kenya
- Single, sends money monthly to her parents in Kisumu and her younger sister's school fees in Eldoret
- Marketing coordinator at a mid-sized Kenyan logistics company — handles social media campaigns, earns roughly KES 85,000/month (~$650 USD)
- iPhone 12 user (bought refurbished; it's the most expensive thing she owns)
- Heavy WhatsApp and TikTok user, light Instagram presence, no Twitter/X, has never visited GitHub in her life
- Weekends: church on Sunday, nyama choma with friends on Saturday, watches Nigerian movies on Showmax
- Has used M-Pesa since she was 16 — it's how she pays rent, splits dinner bills, and sends money home. She thinks of M-Pesa the way Americans think of debit cards: it's just how money works
- A colleague at work told her about Midnight over lunch, describing it as "like WhatsApp but for money, and nobody can see what you send." That's the entirety of what she knows about blockchain [ASSUMPTION—VALIDATE]

### Quotes
- "I sent my mum money through Western Union last month and they took 800 shillings in fees. For what? It's just numbers on a screen."
- "My colleague showed me this crypto app once and it asked me to write down twelve random words and keep them safe forever. I closed it. I don't have time for that."
- "If I lose money trying some new app, that's my sister's school fees gone. I'm not experimenting with money I actually need."
- "Why does every app want permission to my wallet? I just want to send money, not sign a contract."

### Pains
- **Remittance fees eat her income.** Sending KES 10,000 to her parents through Western Union or formal channels costs 5-8% in fees. She sometimes uses informal hawala networks instead, which are cheaper but unreliable and carry their own risks [ASSUMPTION—VALIDATE: actual fee sensitivity threshold and current workaround channels]
- **Crypto onboarding is designed for people who already understand crypto.** Every wallet she's tried (a colleague once walked her through Trust Wallet) confronted her with seed phrases, network selection dropdowns, and gas fee warnings. She didn't understand what any of it meant and felt stupid — which made her angry, not curious
- **Privacy around money is culturally important but digitally absent.** M-Pesa agents can see her transaction history. Family members who have her phone number can request money through M-Pesa and she feels social pressure to send it. She wants to be able to help her parents and sister without every cousin knowing her balance [ASSUMPTION—VALIDATE: strength of privacy motivation vs. convenience motivation]
- **She has zero tolerance for flows that feel risky or confusing.** If an app asks her to do something she doesn't understand, she assumes it's a scam. This is rational — mobile money scams in Kenya are common and she's seen friends get burned
- **Every new dApp asks me to 'connect wallet' and approve a list of permissions I don't understand — scopes, signing rights, proof generation. My banking app never does this.** The "connect wallet" pattern that crypto-native users take for granted is a foreign and alarming interaction for her. She has no mental model for why an app needs "permission" to her money before she's even done anything [ASSUMPTION—VALIDATE: whether wallet-connection friction is a distinct drop-off point vs. just part of general onboarding friction]

### What is This Person Trying to Accomplish?
- Send money to family members in other Kenyan cities (and eventually to her brother working in Dubai) faster and cheaper than Western Union or M-Pesa cross-border transfers
- Keep her financial activity private from extended family and casual acquaintances who might pressure her for loans
- Feel confident that the money she puts into the app is actually safe — not "blockchain safe" in some technical sense she can't verify, but safe the way M-Pesa feels safe: it just works and the money is there when she checks

### Goals
- **Short-term:** Send her first transaction to a family member without needing anyone to explain it to her, and without paying more than 1-2% in fees. Use dApps without understanding what "connecting a wallet" means — it should feel like logging in
- **Medium-term:** Move a meaningful portion of her monthly remittances off Western Union onto a cheaper channel she controls
- **Long-term:** Build a small savings cushion in a stable digital currency that isn't subject to KES inflation (~6-8% annually), accessible only to her [ASSUMPTION—VALIDATE: whether savings/store-of-value is a real goal or projection]

### Attitudes & Influences
- **Decision-Making Authority:** Full authority over her personal financial tools. No one approves her app choices. But she won't install something unless someone she personally trusts has used it first
- **Decision Influencers:** Her colleague who mentioned Midnight (the original referral), her WhatsApp group of close friends (if one of them starts using it, she'll try it), and M-Pesa's brand trust (she'll unconsciously compare everything to M-Pesa's reliability). She does not read tech blogs, follow crypto influencers, or care about decentralization as a concept
- **Beliefs & Attitudes:** Deeply skeptical of anything that feels like a "tech bro" product. She doesn't want to "be her own bank" — she wants to send money cheaply. If the onboarding takes more than 2 minutes or asks her to understand something before proceeding, she'll close the app and never reopen it. She trusts things that look simple, behave predictably, and are used by people she knows. She has a strong mental model from M-Pesa: tap, enter amount, confirm with PIN, done. Anything more complex than that needs a very good reason

---

## Persona 2: Developer Dev

### Name
- Developer Dev

### Bio & Demographics
- 34 years old, lives in Berlin (Kreuzberg), originally from Cologne
- In a long-term relationship, no kids, splits a flat with his partner who works in UX research (they talk about user onboarding at dinner more than either of them would admit)
- Senior full-stack engineer, 10 years experience — primarily TypeScript/Node, some Rust, comfortable with Solidity but doesn't enjoy it
- Currently technical co-founder of a 4-person startup building a privacy-preserving DeFi lending protocol on Midnight. Pre-seed funded, 14 months of runway left
- Active on GitHub (contributions most days), reads Hacker News but rarely comments, has a modest Twitter/X following (~2,400) from threads about ZK proof systems, lurks in the Midnight developer Discord
- Free time: bouldering at a local gym, mechanical keyboard building, re-reading old Neal Stephenson novels for the third time
- Evaluated Midnight's SDK extensively — has worked with the Compact language, understands ZKIR and the constraint system, has benchmarked proof generation times (currently ~18 seconds on his M2 MacBook for a moderately complex circuit), and has strong opinions about what the DX gets right and wrong [ASSUMPTION—VALIDATE: actual developer experience friction points with Midnight SDK]

### Quotes
- "I can explain Poseidon hashing to my team. I cannot explain it to my users. And I shouldn't have to — that's a product failure, not a user failure."
- "We tested our prototype with 12 people. Eight of them bounced at wallet setup. Eight. Our actual lending product is fine — the onboarding is killing us before anyone even sees it."
- "I don't want a wallet SDK that gives me 47 configuration options. I want one function call: `createOnboardedUser()`. Give me back a session token and a human-readable name. Handle the rest."
- "The onboardUser() call is exactly what I wanted. Now I need it to not crash in production."
- "MCP looks like it learned from WalletConnect's mistakes, but I still need a React hook library, not a protocol spec."

### Pains
- **His users are Nadia, and Nadia will not read documentation.** His lending protocol is technically sound, but the target market is regular people in emerging economies who want to borrow against stablecoin collateral without identity exposure. Every extra step in wallet setup is a user he'll never get back. He's watched screen recordings of test users staring at the "back up your seed phrase" screen and literally putting their phone down
- **The SDK onboarding wrapper exists but needs production hardening.** The `onboardUser()` API (Section 5.10) is exactly what he asked for — a single entry point that handles key generation, name registration, credential issuance, and fee subsidization. But he needs production-ready error handling: what happens when the proof server crashes at 14 seconds? What retry logic should he use? Where are the monitoring hooks? The happy path works; the failure modes are what keep him up at night [ASSUMPTION—VALIDATE: specific SDK pain points and whether they match broader developer sentiment]
- **Integrating the Midnight Connection Protocol (MCP) is a significant undertaking for a small team.** MCP (Section 5.9) defines 5 CAIP-25 scopes, progressive authorization, proof generation callbacks, and session management. The spec is thorough and clearly learned from WalletConnect's mistakes, but implementing it correctly is a lot of surface area for a 4-person startup. He needs a React hook library or a reference integration, not just a protocol specification [ASSUMPTION—VALIDATE: whether dApp developers will adopt MCP or build custom connectors]
- **18-second proof times are a UX problem he can't engineer around.** His users expect mobile-app responsiveness (~300ms). An 18-second wait during onboarding feels broken, not "decentralized." He needs the onboarding system to handle proof generation with proper progress indication and parallelization, because he can't make the proofs faster from his side
- **He's burning engineering time on wallet infrastructure instead of his actual product.** His startup has 14 months of runway. Every sprint he spends on onboarding plumbing is a sprint he's not spending on the lending protocol itself. He wants to outsource onboarding to an SDK that just works, the way Stripe made him stop thinking about payment processing

### What is This Person Trying to Accomplish?
- Ship a privacy-preserving lending product that regular people (not crypto natives) can actually use, within the next 6 months
- Reduce his onboarding bounce rate from ~80% to under 20% without building custom wallet infrastructure [ASSUMPTION—VALIDATE: actual bounce rates across Midnight dApps]
- Integrate Midnight's onboarding as a single SDK call in his app so his small team can focus on lending logic, not wallet plumbing

### Goals
- **Short-term:** Production-ready integration with monitoring, error recovery, and proof-generation progress UI — the `onboardUser()` entry point exists, now he needs it hardened for real traffic with proper retry logic, timeout handling, and observability hooks
- **Medium-term:** Get his lending protocol to 10,000 active users who completed onboarding and made at least one transaction, proving that privacy-preserving DeFi can reach a mainstream audience
- **Long-term:** Build a sustainable business on Midnight's ecosystem — which means Midnight's onboarding and developer tools need to be good enough that he'd bet his company on them (he already has, and that keeps him up at night sometimes)

### Attitudes & Influences
- **Decision-Making Authority:** Co-founder and CTO — makes all technical stack decisions. His co-founder (CEO, handles business side) trusts his technical judgment completely. No procurement process; he evaluates, decides, and integrates himself
- **Decision Influencers:** Midnight developer Discord (he watches what other builders are struggling with), Hacker News threads on ZK developer experience, his partner's UX research perspective (she's the one who suggested recording onboarding test sessions), and direct comparison with other L1/L2 developer experiences (he's shipped on Ethereum and Solana before and has strong baseline expectations for SDK quality)
- **Beliefs & Attitudes:** Believes privacy is a fundamental right, not a feature toggle — that's why he chose Midnight over Polygon or Arbitrum. Deeply pragmatic about developer tools: he doesn't care about elegant architecture if the API is painful to use. Judges SDKs by time-to-hello-world, not by whitepaper quality. Suspicious of any blockchain project that talks more about its technology than its developer experience. Has a strong bias toward "convention over configuration" — he'd rather have sensible defaults he can override than a blank canvas he has to configure from scratch

---

## Persona 3: Enterprise Ethan

### Name
- Enterprise Ethan

### Bio & Demographics
- 45 years old, lives in Frankfurt, Germany
- Married, two teenage daughters, coaches their youth football (soccer) team on weekends — it's his only reliable escape from compliance documents
- Head of Compliance at a mid-sized European fintech (~200 employees) that is licensed under MiCA (Markets in Crypto-Assets Regulation) and regulated by BaFin
- 20 years in financial compliance — started at Deutsche Bank in traditional banking compliance, moved to fintech 6 years ago because he saw crypto regulation coming and wanted to be ahead of it rather than reacting to it
- His company is evaluating Midnight for issuing regulated security tokens (tokenized bonds, specifically) to institutional and qualified retail investors
- LinkedIn is his only social media (posts occasionally about regulatory developments, gets decent engagement from compliance peers). Does not use Twitter/X. Reads the Financial Times, BaFin circulars, and FATF guidance documents the way Dev reads Hacker News
- Has sat through approximately 40 blockchain vendor pitches in the last 3 years. He can smell vaporware from the first slide [ASSUMPTION—VALIDATE: actual vendor evaluation volume and selection criteria]

### Quotes
- "Don't tell me your key recovery is 'decentralized' and expect me to smile. Tell me what happens when a retail investor loses their phone, their backup, and calls my support line crying. Then show me the audit trail."
- "I need to present this to BaFin. They will ask me two questions: where is the PII, and who is liable if it leaks. If you can't answer both in one sentence each, we're done."
- "I've seen six 'zkKYC' pitches this year. Five of them were a Groth16 proof wrapped around a centralized database. That's not zero-knowledge — that's just KYC with extra steps."
- "The institutional helper design is sound on paper. Now show me a vendor I can contract with who runs it in an audited TEE."

### Pains
- **FATF Travel Rule compliance is non-negotiable, but privacy-preserving identity is genuinely hard.** The Travel Rule requires that regulated entities share originator and beneficiary information for transactions above €1,000. He needs a system that can satisfy this requirement (provide identity information to counterparty VASPs when legally required) while not creating a centralized PII honeypot that becomes a liability. Most "zkKYC" solutions he's evaluated are privacy theater — the ZK proofs verify against a centralized identity database that still stores everything [ASSUMPTION—VALIDATE: specific regulatory requirements and whether Midnight's attestation tree model actually satisfies FATF Travel Rule as implemented in MiCA]
- **The institutional helper model is specified but no vendor offers it as a managed service.** The institutional recovery design (Section 10.5) addresses his core concern — TEE-based helpers with auditable recovery procedures and clear liability boundaries. The architecture is sound: it replaces "ask your friends to hold your key shares" with a professional, contractual relationship. But no vendor currently offers this as a managed service. He can't contract with a spec. He needs a provider he can put through his vendor due diligence process, negotiate an SLA with, and hold accountable when something goes wrong [ASSUMPTION—VALIDATE: whether institutional helper vendors will emerge, and whether existing custody providers (Fireblocks, Copper, etc.) will adapt to offer this]
- **He's caught between innovation pressure and regulatory conservatism.** His CEO wants to move fast on tokenized securities (competitors are launching). His board wants regulatory certainty. He's the person who has to find a technology stack that satisfies both, and right now he's not confident any blockchain platform does. Midnight's privacy architecture is the most promising he's seen, but "promising" isn't the same as "audit-ready" [ASSUMPTION—VALIDATE: actual competitive pressure timeline and board risk appetite]
- **Credential lifecycle management exists in the spec but lacks operational tooling.** Expiration and revocation are specified via Merkle root versioning (Section 8.7), which is architecturally clean — credentials can be expired or revoked without revealing the holder's identity. But there's no compliance dashboard, no batch query API for checking credential status across a portfolio of investors, and no integration with his existing sanctions screening vendor. He needs to wire this into his compliance toolchain, not read about it in a whitepaper [ASSUMPTION—VALIDATE: whether Merkle root versioning satisfies specific MiCA credential lifecycle requirements and audit expectations]
- **Vendor lock-in with a blockchain startup terrifies him.** If Midnight's core team disappears or pivots, his company has issued regulated securities on infrastructure it can't maintain. He needs to understand governance, open-source commitments, and the upgrade path before any of the technical details matter

### What is This Person Trying to Accomplish?
- Complete a technical and regulatory due diligence assessment of Midnight's onboarding and identity stack, producing a document he can present to BaFin and his board that demonstrates regulatory compliance
- Establish that Midnight's zkKYC implementation satisfies MiCA's identity verification requirements and FATF Travel Rule obligations without creating impermissible PII storage
- Confirm that key recovery (DeRec) produces an auditable process with clear custodial liability boundaries — meaning he can show regulators exactly what happens in every failure scenario and who is responsible at each step

### Goals
- **Short-term:** Procure an institutional recovery service provider who can operate TEE-based helpers under a contractual SLA, and integrate credential lifecycle management (expiration, revocation, status queries) with his existing compliance toolchain — including his sanctions screening vendor and internal audit reporting
- **Medium-term:** Produce an internal compliance assessment that recommends (or rejects) Midnight as the platform for his company's tokenized bond issuance program. This assessment needs to satisfy BaFin's supervisory expectations and survive scrutiny from external auditors
- **Long-term:** If Midnight passes evaluation, build a compliance-ready identity and wallet stack that becomes a competitive advantage — his company becomes the fintech that solved privacy-preserving regulatory compliance, not the one that got fined for a data breach or the one that was too slow to tokenize

### Attitudes & Influences
- **Decision-Making Authority:** Does not make the final go/no-go decision (that's the CEO and board), but his recommendation is effectively a veto. If he says "the compliance risk is unacceptable," the project dies. If he says "I'm comfortable with the risk profile," the project proceeds. He controls the most important gate in the evaluation process
- **Decision Influencers:** BaFin's published guidance and informal supervisory dialogue (he has relationships with specific supervisors), FATF updated guidance on virtual assets, peer compliance officers at other MiCA-licensed firms (they share notes informally at industry events), external legal counsel (Clifford Chance, who his company retains for crypto-regulatory opinions), and the European Blockchain Association's technical standards working group (he attends quarterly)
- **Beliefs & Attitudes:** Believes blockchain has genuine utility for regulated finance but thinks 90% of the industry is dangerously cavalier about compliance. Respects technical depth — he can read a whitepaper and evaluate cryptographic claims, though he's not a developer. Deeply suspicious of "trustless" rhetoric; his entire professional existence is about establishing clear trust relationships with clear liability. Prefers working with teams that acknowledge their limitations honestly over teams that claim their technology solves everything. Will not sign off on anything he can't explain to a regulator in plain language. Views privacy and compliance as compatible but only when the implementation is rigorous — hand-waving about "zero-knowledge proofs" without showing the specific circuit design and threat model is a red flag, not a selling point

---

## Cross-Persona Relationships

These three personas are not independent — they form a dependency chain that mirrors the product's adoption funnel:

1. **Nadia** is the end user. If onboarding doesn't work for her, nothing else matters.
2. **Dev** is the channel. His dApp is how Nadia encounters Midnight. If the SDK is too painful, he builds on a different chain and Nadia never sees Midnight at all.
3. **Ethan** is the gatekeeper. If Midnight can't satisfy his compliance requirements, regulated financial products (the highest-value use case) never launch, and the ecosystem stays niche.

The onboarding system has to work for all three simultaneously: invisible to Nadia, one-call simple for Dev, auditable for Ethan. Any design that optimizes for one persona at the expense of another will fail.

### Key Validation Priorities

| Persona | Highest-Priority Assumption to Validate | Suggested Method |
|---------|---------------------------------------|------------------|
| Nadia | 2-minute tolerance threshold and seed phrase abandonment rate | Usability testing with M-Pesa users in Nairobi (n=15-20) |
| Dev | 80% onboarding bounce rate and SDK fragmentation pain | Developer survey + instrumented onboarding funnel analytics across existing Midnight dApps |
| Ethan | Whether Midnight's attestation tree model satisfies FATF Travel Rule under MiCA implementation | Structured interview with 3-5 compliance officers at MiCA-licensed firms + external legal opinion |
