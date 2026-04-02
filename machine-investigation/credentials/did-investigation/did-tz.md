# did:tz (Tezos)

| Property | Value |
|----------|-------|
| Specification | [Spruce](https://did-tezos.spruceid.com) / [GitHub](https://github.com/spruceid/did-tezos) |
| Organization | Spruce Systems, Inc. |
| DID Format | `did:tz:[network:]<tezos-address>` |

## Overview
did:tz is a multi-modal DID method for the Tezos blockchain, supporting tz1, tz2, tz3 (account addresses from different curves) and KT1 (smart contracts). It offers both implicit (generative like did:key) and on-chain (via DID Manager smart contracts) modes, plus off-chain options.

## Evaluation

### 1. Feasibility/Complexity
**Partially self-contained.** The method has interesting multi-modal design:

- **Implicit mode**: Like did:key, generates DID document from address without blockchain
- **On-chain mode**: Requires DID Manager smart contract (TZIP-19) deployment
- **Off-chain mode**: Supports Sidetree, KERI, or DHT-based updates

For full resolution with key rotation, a DID Manager contract is needed. Implicit mode allows basic identity without blockchain but cannot derive public key from address alone.

### 2. Ecosystem
**Medium.** Active Tezos ecosystem. Spruce is a credible identity company. DIDKit support. W3C CCG work item.

### 3. Stability
**Moderate.** Well-documented specification. Multiple resolution options provide flexibility. Depends on Tezos blockchain for on-chain features.

## Recommendation
**Requires investigation**

The implicit mode (similar to did:key) may work without blockchain for basic identity, but practical use with key rotation and service endpoints requires Tezos blockchain. The multi-modal design is interesting but adds complexity. Further investigation needed to determine if implicit mode alone meets requirements.
