# Problem Framing Canvas: Midnight Wallet Onboarding

**Version:** v0.2
**Date:** 2026-03-29
**Facilitator:** Product Management
**Participants:** Protocol Engineering, UX Research, Security Architecture, Developer Relations

---

## Phase 1: Look Inward

### What is the problem? (Symptoms)

Users abandon self-custodial wallet setup at rates that should embarrass this industry.

The observable symptoms: onboarding completion rates for self-custodial wallets hover somewhere between 20-40%, depending on whose numbers you believe and how generously they define "completed." For a privacy chain like Midnight, it is almost certainly worse -- we are asking users to navigate everything that makes a normal crypto wallet confusing, plus zero-knowledge proof generation latency, plus the conceptual overhead of shielded vs. unshielded tokens, plus a three-wallet architecture they did not ask for.

Concretely, the failure modes look like this:

- A user downloads a wallet app. They are shown 24 words and told their entire financial future depends on writing them down correctly. Some percentage close the app and never come back.
- A user completes setup but needs to buy a gas token before they can do anything. They navigate to an exchange, create a second account, pass KYC, buy tokens, figure out the right network, copy a hex address, and send. Many do not make it through.
- A user who does make it through onboarding faces a second wall when they try to use a Midnight dApp that requires identity attestation. Now they need zkKYC, which means another verification flow, another concept to understand, another place to drop out.
- Developers build dApps on Midnight and then watch their user funnels collapse at step one: "connect wallet."

The chain of consequences is not subtle. Low onboarding completion means low active users. Low active users means low transaction volume. Low transaction volume means low fee revenue, weak network effects, and a privacy set that is too small to actually provide privacy. The onboarding problem is not a UX inconvenience -- it is an existential threat to the network.

### Why haven't we solved it?

Several reasons, all reinforcing each other:

- **The industry treats complexity as a virtue.** Seed phrases became the standard not because they are good UX but because they signal self-sovereignty. The crypto community has spent a decade telling itself that if setup is painful, it means you are doing security correctly. Hardware wallet companies literally sell the complexity. This cultural inertia is real and it is strong.

- **Privacy adds a second layer of difficulty that nobody has cracked.** Even the best "seedless" wallet projects (Privy, Web3Auth, Lit Protocol) do not address ZK-specific UX problems: proof generation takes time, shielded transactions behave differently from unshielded ones, and identity attestation is a concept with no mainstream mental model. Nobody has shipped a production wallet that does seedless + privacy + identity + naming together.

- **TEE constraints fragment the solution space.** iOS Secure Enclave only supports P-256. Android StrongBox capabilities vary by manufacturer. Laptops have TPM or ARM CCA or nothing. Any design that relies on hardware security has to deal with this fragmentation, which makes the engineering problem genuinely hard -- not just "hard because nobody tried."

- **Incentives are misaligned.** Centralized exchanges benefit enormously from self-custodial wallet complexity. Every user who gives up on self-custody is a potential Coinbase or Binance customer. The entities with the most resources to solve this problem have the least motivation to solve it.

- **It is a multi-system integration problem.** Key management, naming, identity, fee subsidization, and recovery are each individually complex. Getting any one of them right is a project. Getting all five to work together in a single flow is why this remains unsolved.

> **v0.2 update:** Since v1 of this canvas, the design now includes an SDK onboarding wrapper (`midnight.onboard()`), institutional recovery helpers, credential lifecycle management, and a dApp connection standard. These are specified but unshipped and untested. The gap has shifted from "no solution exists" to "the solution is a spec, not running code."

### How are we part of the problem? (Assumptions & biases)

This is the uncomfortable section. Here is where we are probably fooling ourselves:

- **We are cryptographers building for cryptographers.** Our team thinks in BLS12-381 scalar fields and Poseidon hash domains. We designed a six-layer architecture with distinct trust boundaries because that is how security engineers think. Users do not think in layers. They think in tasks: "I want to send money to my friend." The six-layer model might be exactly the right internal architecture, or it might be four layers too many for an MVP. We genuinely do not know yet, and that uncertainty should bother us more than it does.

- **We assume users want self-custody.** This is the big one. A large percentage of crypto users -- maybe the majority -- would happily delegate custody to a trusted institution if the alternative is managing their own keys. We are building a sophisticated seedless self-custodial system. But have we validated that our target users actually want self-custody, or are we imposing our values on them? The DeRec recovery model is better than seed phrases, but it still requires users to designate five trusted helpers. That is a social coordination problem. Some users do not have five people they trust with financial recovery.

- **We assume "scan a QR code" is simple.** It is simple for a 30-year-old with a modern smartphone who has used QR codes for restaurant menus and boarding passes. It is not simple for a 70-year-old user. It is not simple for a user with motor impairments who struggles to hold a phone steady. It is not simple for a user whose phone camera does not work well. We picked QR scanning because it is elegant for the demo, and we may have under-indexed on accessibility.

- **We are designing for smartphone owners with government ID.** The design requires a TEE-capable device (modern smartphone or laptop) and, for zkKYC, a government-issued identity document. This implicitly excludes users without smartphones, users without government ID (refugees, undocumented immigrants, unbanked populations in some emerging markets), and minors. We say we care about emerging markets, but the design assumes hardware and documentation that not everyone has.

- **We have not talked to enough non-crypto users.** Our user research skews toward people who already understand blockchain concepts. The truly hard onboarding problem is not "how do we make it easier for someone who knows what a wallet is" -- it is "how do we onboard someone who does not know what a blockchain is and does not care." We are not sure we have enough signal from that population.

- **We assume dApp developers will adopt our Midnight Connection Protocol (MCP) standard instead of building custom integrations.** The history of wallet standards (EIP-1193 fragmentation, CIP-30 extension sprawl) suggests adoption is not guaranteed.

- **We assume an institutional TEE-based recovery helper satisfies enterprise compliance.** No regulator has actually reviewed this model. We are designing in a vacuum.

**Which of these might be redesigned, reframed, or removed?**

The self-custody assumption deserves the most scrutiny. There may be a hybrid model: default to a custodial-like experience (institutional key management) for users who want simplicity, with a progressive path to full self-custody for users who grow into it. The QR-scan assumption should be tested with accessibility audits and alternative entry points (deep link, NFC tap, manual pairing code). The six-layer architecture is almost certainly correct internally, but the user-facing experience should expose zero of those layers -- if we are debating how many layers to show users, we have already lost.

---

## Phase 2: Look Outward

### Who experiences the problem?

**Who:**
- **First-time crypto users** -- people who heard about Midnight from a friend, a news article, or an employer and want to try it. They have no existing mental model for wallets, keys, or blockchain transactions. They are the largest addressable market and the hardest to serve.
- **Non-technical users in any market** -- small business owners, gig workers, freelancers who might benefit from privacy-preserving payments but cannot navigate current crypto UX. They do not care about decentralization as an ideology; they care about getting paid.
- **Users in emerging markets (Africa, Southeast Asia, Latin America)** -- populations where mobile money is already normal but crypto onboarding is not. They often have lower-end devices, intermittent connectivity, and limited access to traditional financial infrastructure. For these users, the onboarding barrier is compounded by device constraints and data costs.
- **Enterprise compliance teams** -- organizations that need regulated access to privacy-preserving blockchain. They need zkKYC to work, they need audit trails, they need managed onboarding for employees. Their problem is not "I cannot set up a wallet" but "I cannot deploy wallets to 500 employees without a helpdesk disaster."
- **dApp developers** -- their problem is indirect but severe. Every user who fails to onboard is a user who never reaches the dApp. Developers experience the onboarding problem as a conversion rate problem: 1,000 people click "try this dApp" and 200 make it through wallet setup. The developer did not create the onboarding problem, but they pay the cost.
- **dApp developers who must integrate the Midnight Connection Protocol** -- to offer wallet connectivity, they must adopt and implement MCP. They inherit our onboarding quality as their user acquisition cost.
- **Institutional recovery service operators** -- a new market role created by the design (Section 10.5) that doesn't exist yet as a vendor category. These would be organizations running TEE-based recovery helper infrastructure for enterprise clients, but no one has built or offered this service.

**When/Where:**
- At the moment of first contact: someone clicks a link, scans a code, or downloads an app
- On mobile devices (80%+ of target users in emerging markets)
- Often in contexts with limited time and attention -- a user is trying to complete a transaction, not learn about key management
- During regulated interactions where identity verification is required (financial services, age-gated content, institutional access)

**Consequences:**
- Users abandon the flow and form a negative impression of the entire ecosystem ("crypto is too complicated for me")
- Network effects stall: privacy sets remain small, transaction volume stays low, developer ecosystem under-invests
- Users who do complete setup but do not understand recovery end up losing access to funds -- a worse outcome than never onboarding at all
- Emerging-market adoption is captured by centralized services (Binance, local custodial providers) that offer easier onboarding at the cost of sovereignty and privacy

**Lived experience varies:**
A crypto-comfortable developer in San Francisco experiences onboarding as a mild annoyance -- fifteen minutes of friction before they get to the interesting part. A market vendor in Lagos experiences it as an impenetrable wall. A compliance officer at a European bank experiences it as a risk management nightmare. The same technical barrier produces wildly different human consequences depending on context, technical fluency, device capability, and what is at stake.

### Who else has this problem?

**Who else:**
- **Every L1 and L2 blockchain** -- Ethereum, Solana, Cardano, Polygon all struggle with onboarding. The pattern is universal: download wallet, write seed phrase, buy gas, copy address, make first transaction. Nobody has fully solved it, though some have made progress on individual components (smart contract wallets on Ethereum eliminate seed phrases but add gas abstraction complexity; Solana's Saga phone pre-installs a wallet but only works on one device).
- **Traditional fintech during early mobile banking adoption** -- in 2010-2015, mobile banking apps had terrible activation rates too. They solved it through progressive disclosure, institutional trust, and relentless iteration. Crypto has not had its "Venmo moment" yet.
- **Enterprise SaaS onboarding** -- complex B2B products face similar activation challenges. The playbook there (guided setup wizards, white-glove onboarding, progressive feature exposure) has relevant lessons.
- **Identity verification providers broadly** -- the friction of KYC is not unique to crypto. Every fintech, neobank, and regulated app struggles with identity verification drop-off. Plaid, Stripe Identity, and Jumio have spent years optimizing this funnel.

**How they deal with it:**
- Smart contract wallets (Safe, Argent) abstract seed phrases but still require gas management
- Account abstraction (ERC-4337) allows gasless transactions but adds smart contract deployment complexity
- Social login wallets (Privy, Web3Auth) use MPC key sharding behind OAuth flows -- closest to what we are proposing, but none handle privacy or identity attestation
- Custodial exchanges punt the problem entirely by holding keys for users

### Who doesn't have it?

- **Crypto-native power users** -- people who have MetaMask on three devices, a Ledger in a safe, and strong opinions about seed phrase storage. For them, onboarding friction is a feature, not a bug -- it filters out "tourists."
- **Hardware wallet enthusiasts** -- Ledger and Trezor users who have internalized the complexity and view it as necessary security hygiene.
- **Users of custodial services** -- Coinbase, Binance, and Cash App users who traded self-custody for simplicity. They onboarded in minutes because someone else manages the keys. They do not have the onboarding problem; they have a different problem (counterparty risk, censorship vulnerability) that they may not know about yet.
- **Institutional users with dedicated IT support** -- large organizations that deploy managed wallet infrastructure. They solve the onboarding problem with money and headcount, not with better UX.

What is different about these groups: either they have already absorbed the complexity cost (power users), they have outsourced it (custodial users), or they have thrown resources at it (institutions). None of these paths scale to the next billion users.

### Who's been left out?

- **Users without smartphones** -- feature phone users in parts of Africa and South Asia. Our design requires a TEE, which requires a modern smartphone. USSD-based mobile money (M-Pesa) reaches these users; we do not.
- **Users with accessibility needs** -- visual impairments (QR scanning requires sight), motor impairments (holding a phone steady for QR scan), cognitive disabilities (multi-step flows with abstract concepts). We have not done an accessibility audit of the proposed flow.
- **Elderly users** -- not a monolith, but users over 65 statistically have lower comfort with QR codes, biometric authentication, and app-based workflows. Our "scan and go" flow may not be their flow.
- **Users in restricted app store countries** -- China, Iran, and other countries where Google Play and Apple App Store access is limited or surveilled. How do they get the wallet app in the first place?
- **Users without government-issued ID** -- approximately 850 million people worldwide lack official identification (World Bank ID4D data). Our zkKYC layer requires government ID for the initial verification. These users can create a wallet but cannot get identity attestations, which locks them out of regulated dApps.
- **Minors** -- under-18 users who cannot pass KYC but may have legitimate reasons to use privacy-preserving technology (allowance management, educational use, protection from data harvesting).
- **Users with low literacy or limited digital literacy** -- the flow assumes the user can read instructions, understand prompts, and navigate a smartphone app. This is not universal.

### Who benefits?

**When the problem persists (status quo beneficiaries):**
- **Centralized exchanges** -- Coinbase, Binance, Kraken. Every user who fails at self-custodial onboarding is a potential custodial customer. Coinbase's entire value proposition is "we make crypto easy" -- and that proposition only works if self-custody is hard. They have no incentive to solve this for us.
- **Hardware wallet companies** -- Ledger and Trezor sell the complexity narrative. "Your keys, your responsibility" is their marketing tagline. If onboarding became trivially easy, it would undermine their positioning as the "serious" option.
- **KYC/AML compliance vendors** -- companies that profit from repeated identity verification across platforms (Jumio, Onfido, Chainalysis). A portable, reusable zkKYC credential threatens their per-verification revenue model.
- **Incumbent financial institutions** -- banks and payment processors that compete with crypto for everyday transactions. Difficult onboarding keeps crypto as a niche asset class rather than a payment rail.
- **Embedded wallet SDK vendors (Privy, Dynamic, Thirdweb)** -- if Midnight ships its own `onboardUser()` wrapper, these vendors lose a potential integration market. Their business model depends on protocols not having turnkey onboarding.

**When the problem is solved (who benefits, who loses):**
- **Midnight's network effects accelerate** -- more users means larger privacy sets, more transaction volume, more developer interest, more fee revenue. This is the core strategic reason to solve the problem.
- **dApp developers get viable user funnels** -- applications that currently cannot grow because users cannot get past "connect wallet" suddenly have a chance.
- **Users in emerging markets gain access to privacy-preserving financial infrastructure** -- this is the social impact case, and it is real.
- **Centralized exchanges lose their UX moat** -- if self-custodial onboarding matches custodial ease, the "convenience vs. sovereignty" tradeoff collapses. This is not just a competitive benefit for Midnight; it shifts the power dynamic across the entire industry.
- **KYC vendors lose per-verification revenue** -- if zkKYC credentials are portable and reusable, the "verify once, prove everywhere" model eliminates repeated identity checks. Good for users, bad for verification vendors' top line.

---

## Phase 3: Reframe

### Stated another way, the problem is:

Non-technical users -- and especially users in emerging markets, users without deep crypto knowledge, and users whose first interaction with blockchain is through a specific application they want to use -- cannot get from "I heard about this" to "I am using this" on a privacy-preserving blockchain, because the current onboarding path demands that they understand concepts they have never encountered (seed phrases, gas tokens, key management, shielded vs. unshielded transactions, zero-knowledge proofs as a user-facing concept) and perform tasks that have no analogue in their existing digital experience.

This leads to 60-80% abandonment before first transaction, which starves the network of the users it needs for privacy guarantees to actually work. The problem has persisted because the crypto industry conflates complexity with security, because privacy-preserving systems add a second layer of UX difficulty that even the best "seedless wallet" projects have not addressed, and because the teams building these systems (including ours) are cryptographers who instinctively optimize for architectural elegance over onboarding completion rates.

The populations most affected -- first-time users, emerging-market users, elderly users, users without government ID -- are precisely the populations that stand to benefit most from privacy-preserving financial infrastructure. We are building the system they need while making it inaccessible to them.

### How Might We...

**How might we** take a new Midnight user from zero state to productive dApp interaction -- from first QR scan through ongoing wallet connections -- in under 60 seconds and under 3 conscious decisions, with 80%+ completion rate across non-technical populations, while maintaining self-custodial ZK-proven security and regulatory compliance?

---

## Assumptions to Validate

Before moving to solutions, these assumptions need direct testing with target users:

1. **"Scan a QR code" is an acceptable entry point for our target demographics.** Test with users over 60, users in emerging markets with lower-end devices, users with accessibility needs.
2. **Users will designate 5 recovery helpers.** This is a social coordination requirement. Validate that target users have 5 people they would trust and that those people would accept the role.
3. **40-60 second wait time for ZK proof generation is tolerable.** For users accustomed to instant app onboarding, a minute of waiting may feel broken. Test different progress indication strategies.
4. **Users do not need to understand self-custody to benefit from it.** Our design hides key management. Validate that this abstraction does not create false expectations (e.g., "where is the customer support number if I lose my phone?").
5. **zkKYC verification can be completed in the same flow.** Adding identity verification to initial onboarding may be one step too many. Consider whether it should be deferred to first regulated interaction.
6. **Users will tolerate a ~60-second commit-reveal wait during name registration without abandoning the flow.** The customer journey map (Section 15) shows this as a distinct patience test separate from proof generation. Users who survived the first wait may not survive a second.
7. **Framing zkKYC as "optional" won't cause >50% of users to skip it when they'll later need it for high-value transfers.** If most users skip, the credential ecosystem remains thin and dApps requiring attestation face the same empty-funnel problem we started with.
8. **Selecting 5 recovery helpers feels empowering rather than burdensome.** The M-Pesa chama model suggests yes -- people in communal financial cultures are accustomed to selecting trusted circles. But Midnight's technical framing ("cryptographic recovery helper") may break the analogy that makes it intuitive.

---

## Next Steps

1. **Validate with users:** Run moderated onboarding tests with non-crypto users across at least three demographics (young urban, emerging-market mobile, 60+ age group). Measure where they drop and why.
2. **Accessibility audit:** Engage accessibility specialists to evaluate the QR-scan flow and identify alternative entry points for users with visual, motor, or cognitive accessibility needs.
3. **Explore progressive custody model:** Investigate whether a custodial-to-self-custodial gradient (start managed, progressively decentralize key control) better serves users who are not ready for full self-custody on day one.
4. **Generate solutions:** Use opportunity-solution tree to map the validated problem to specific solution options, including the QR-scan flow from the design doc and alternatives we have not considered yet.
5. **Scope the MVP:** The design doc describes 6 layers, 8 investigation streams, and at least 15 Compact contracts. What is the minimum slice that delivers the core onboarding experience? This is the question the problem framing sets up but does not answer.
