# MVE-Planning (Minimum Viable Experience)

This document is meant to track investigations into the end-goal user/developer experience for the Midnight OS, which drives requirements for

- Nightstream
- Starstream mock-ledger

This repository is split into the following folders:

- [methodology](./methodology/): represents _human written_ and _approved_ directions for the projects. The goal of this folder is to be very explicit documents for both humans and AI about the goal of this repo and the context for how content should be written for it.
- [human-investigation](./human-investigation/): represents _human written_ investigation results. These are documents that have been written by humans, have been presented to the team, have been checked for accuracy, and are approved as the result of an investigation.
- [references](./references/): represents _external_ _human written_ reference information from other systems that benefit from being inlined into this repository.
- [machine-investigation](./machine-investigation/): represents _ai written_ investigation results. Contents in this repo may provide helpful context in specific scenarios, but they should not be fully trusted for accuracy, completeness, and may not fully align with project goals (in fact, the point of these documents is often to not be oriented to the project goals at all, but rather provide data dumps from which humans and AIs can continue)

For those new to the repo, reading [methodology](./methodology/) is recommended.

### MPC

MPC is useful to implement the following concepts:

- Key creation
- Key recovery
- Restoring private state for dApps
- dApp-specific usage
  - games (ex: shuffling cards)
  - DeFi (ex: order matching in a private DEX)
  - Oracle/identity (zkTLS)

We have the following investigations:

- [ ] NEAR MPC
- [ ] Decrec
- [ ] Taceo
- [x] Which MPC is good for Nightstream in general [investigation](./human-investigation/mpc.md)

### Account abstraction

- [ ] NEAR key
- [~] Tempo account abstraction [investigation](./human-investigation/tempo-passkeys.md)
- [ ] Worldcoin
- [x] zkLogin [investigation](./human-investigation/zkLogin.md)
- [x] Sui Passkeys [investigation](./human-investigation/sui-passkeys.md)
- [ ] Aptos Keyless
- [ ] Lace SDK
- [ ] MoonPay OpenWalletStandard(OWS)

### Identity

- [ ] did:web with zkTLS
- [ ] Blindfold for ID vs Ligero
- [ ] Add credential to Apple Wallet feasibility
- [ ] zkME (and other groth16 approaches?)
- [ ] Handling metadata associated with the key (ex: which dApps they have installed, private state for those dApps, etc.)
- [ ] Associating data in a smart contract OCI registry with DIDs
- [x] OpenID4VCI + Digital Credentials API [investigation](./human-investigation/openid4vci-dc-api.md)

### Misc

- [ ] Kubernetes ideas for local registry
