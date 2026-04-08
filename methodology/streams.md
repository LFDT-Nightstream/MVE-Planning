# Work streams

This document tracks progress on various work streams

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

We have the following investigations:
- [ ] NEAR MPC
- [ ] [DeRec Alliance](https://derecalliance.org/) 
- [ ] Taceo
- [x] Which MPC is good for Nightstream in general [investigation](./human-investigation/mpc.md)

### Account abstraction
- [ ] NEAR key
- [~] Tempo account abstraction [investigation](./human-investigation/tempo-passkeys.md)
- [ ] Worldcoin
- [x] zkLogin [investigation](./human-investigation/zkLogin.md)
- [ ] SyRA OAuth login [paper](https://eprint.iacr.org/2024/379), [code](https://github.com/docknetwork/crypto/tree/main/syra)
- [ ] Aptos Keyless
- [ ] Lace SDK
- [ ] MoonPay [OpenWallet Standard](https://openwallet.sh/)(OWS)

### Chain Abstraction
- [ ] [CAKE Framework](https://frontier.tech/the-cake-framework)

### Identity
- [ ] did:web with zkTLS
- [ ] Blindfold for ID vs Ligero
- [x] Add credential to Apple Wallet feasibility
- [ ] [zkMe](https://docs.zk.me/hub) (and other groth16 approaches?)
- [ ] Handling metadata associated with the key (ex: which dApps they have installed, private state for those dApps, etc.)
- [ ] Associating data in a smart contract OCI registry with DIDs
- [ ] [ENS](https://ens.domains/)
- [ ] NEAR [Named Addresses](https://docs.near.org/protocol/accounts-contracts/account-id#named-address)

### Misc
- [ ] Kubernetes ideas for local registry
- [ ] TEEs
- [ ] Midnight Platform

### Research
- [ ] Lattice-friendly DA
- [ ] Lattice-friendly address scheme
- [ ] Lattice-friendly MPC
- [ ] Lattice-friendly PCS
- [ ] Lattice-friendly Dory
- [ ] Lattice-friendly anonymous credentials
- [ ] Folding-friendly Twist & Shout
- [ ] Blindfold for Neo
- [ ] Tensor generalization of lattices (esp. in the context of hardware acceleration)
- [ ] Settle Nightstream via KZG
