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
- [x] SyRA OAuth login [investigation](./human-investigation/SyRA.md)
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
    - Possibly leverage ideas from [ZODA](https://eprint.iacr.org/2025/034)
- [ ] Lattice-friendly address scheme
- [ ] Lattice-friendly MPC
- [ ] Lattice-friendly PCS
- [ ] Lattice-friendly Dory
    - Ideas for this in [SuperNeo](https://eprint.iacr.org/2026/242)
- [ ] Lattice-friendly anonymous credentials
  potential papers:
    - [Practical Post-Quantum Signatures for Privacy](https://eprint.iacr.org/2024/131)
    - [A Framework for Practical Anonymous Credentials from Lattices](https://eprint.iacr.org/2023/560.pdf)
        - an implementation is described in [LaZer](https://eprint.iacr.org/2024/1846.pdf), and implemented [here](https://github.com/lazer-crypto/lazer/blob/10eafeca4cd53ff4fc54193dce904dbd0026fefd/README#L83)
        - video [here](https://www.youtube.com/watch?v=wm2qJyaxyRw&t=1409s)
        - An implementation of an accumulator (lattice version of [CL](https://eprint.iacr.org/2001/019.pdf)) based on LaZer found in [Lattice-Based Accumulator and Application to Anonymous Credential Revocation](https://eprint.iacr.org/2025/1099)
        - Lattice version of BBS based on this technique appears in [Lattice-based Proof-Friendly Signatures from Vanishing Short Integer Solutions](https://eprint.iacr.org/2025/356)
    - BlindFold (introduced in [HyperNova: Recursive arguments for customizable constraint systems](https://eprint.iacr.org/2023/573), used in [Vega](https://eprint.iacr.org/2025/2094) and implemented in Jolt)
    - Blind signatures in general in the lattice setting
- [ ] Privacy in the lattice setting
- [ ] Folding-friendly Twist & Shout
- [ ] Tensor generalization of lattices (esp. in the context of hardware acceleration)
- [ ] Settle Nightstream via KZG
- [ ] Dynamic step size for folding (per-user/per-contract/etc)
