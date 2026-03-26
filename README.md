# MVE-Planning (Minimum Viable Experience)

This document is meant to track investigations into the end-goal user/developer experience for the Midnight OS, which drives requirements for
- Nightstream
- Starstream mock-ledger

## Pending investigations

### MPC

MPC is useful to implement the following concepts:
- Key creation
- Key recovery
- Restoring private state for dApps
- dApp-specific usage
  - games (ex: shuffling cards)
  - DeFi (ex: order matching in a private DEX)
  - Oracle/identity (zkTLS)

#### Investigations
- [ ] NEAR MPC
- [ ] Decrec
- [ ] Taceo
- [ ] Which MPC is good for Nightstream in general

### Account abstraction
- [ ] NEAR key
- [ ] Tempo account abstraction
- [ ] Worldcoin
- [ ] zkLogin
- [ ] Aptos Keyless
- [ ] Lace SDK
- [ ] MoonPay OpenWalletStandard(OWS)

### Identity
- [ ] did:web with zkTLS
- [ ] Blindfold for ID vs Ligero
- [ ] Add credential to Apple Wallet feasibility
- [ ] zkME (and other groth16 approaches?)
- [ ] Handling metadata associated with the key (ex: which dApps they have installed, private state for those dApps, etc.)

### Misc
- [ ] Kubernetes ideas for local registry
