# MVE onboarding

**Problem Statement**

Onboarding new users onto privacy focused blockchain platforms like Midnight is an overwhelming and fragmented experience that loses the majority of users before they complete a single meaningful action. The standard blockchain onboarding funnel forces people through wallet setup, seed phrase management, gas token acquisition, and network configuration before they experience any value, requiring them to manage raw addresses across multiple wallet layers and navigate identity verification just to perform their first transaction.

**This complexity creates several compounding failures:**

**Key management is an obstacle to end users.** Cryptographic seeds must be generated, backed up and secured yet existing wallets expose mnemonic phrases directly, placing the burden of secure storage on non-technical users. Losing a seed means total, irreversible loss of funds and identity. There is no "forgot password" equivalent.

**Multi-wallet architecture leaks complexity.** Midnight's three-token model (Shielded, Night, and Dust) requires three distinct address types derived from different key roles. No existing wallet design unifies these behind a single, human-readable identity forcing users to understand system internals before they can act.

**Identity and privacy are at odds.** Traditional KYC models require every service to collect and store personally identifiable information, creating surveillance infrastructure. Users cannot selectively disclose identity attributes without revealing everything and no system ties privacy-preserving credentials to on-chain identity in a zero-knowledge manner.

**Cross-chain interaction requires chain-specific expertise.** Users must manage separate keys, addresses and fee tokens for each chain, understand bridging mechanisms, and track assets across ecosystems all manually.

**Recovery is nonexistent.** Single-device, single-key account models mean a lost or compromised device results in permanent account loss, with no protocol-level mechanism for social or decentralized recovery and no path to identity continuity across devices.

**The experience feels like a migration, not an upgrade.** Users are asked to abandon familiar authentication patterns like email, biometrics, social login, passkeys and adopt an entirely foreign system. Instead of gaining new capabilities, they feel they've switched ecosystems entirely. The result is that users churn before they ever reach the "aha" moment that demonstrates Midnight's actual value.

**Supporting Evidence:**

- 68% of first-time visitors abandon the "connect wallet" step. Before they even get into the product, most people leave when they see the wallet connection screen. This is the single biggest leak in the entire Web3 funnel.
- 80% of users are lost before their first transaction when UX abstraction is absent. Without account abstraction & gas sponsorship the standard blockchain onboarding funnel loses 4 out of 5 users before they ever transact.
- Mobile-first apps using embedded wallets report 30–40% higher onboarding completion when wallets are created automatically in the background (no seed phrase, no extension), significantly more users make it through.