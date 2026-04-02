# did:vertu (VERTU)

| Property | Value |
|----------|-------|
| Specification | [VERTU](http://did.vertu.com/spec/) |
| Organization | V2 (VERTU) |
| DID Format | `did:vertu:<method-specific-id>` |

## Overview
did:vertu is a DID method developed by VERTU, known for luxury smartphones. The method is part of their Web3 OS ecosystem for Metavertu devices. Private data is stored locally with only hash values published on-chain. The system uses a distributed ledger for DID management and runs a light Ethereum client as a system service for blockchain interaction.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Tied to VERTU's proprietary ecosystem:
- Uses VERTU's distributed ledger infrastructure
- Runs light Ethereum client for blockchain verification
- Part of VERTU's Web3 OS mobile platform
- Designed for VERTU smartphone hardware (Metavertu devices)
- I-DID protocol links phone IMEI to decentralized identifiers

### 2. Ecosystem
Very narrow ecosystem. Designed specifically for VERTU luxury smartphone users. The Metavertu platform is a niche product targeting high-net-worth individuals interested in Web3. Not intended for general-purpose identity infrastructure.

### 3. Stability
The specification is hosted on VERTU's domain. Stability depends entirely on VERTU's continued operation and support for their Web3 initiatives. Hardware-bound identity creates vendor lock-in concerns.

## Recommendation
**No-go**

Proprietary method tied to VERTU hardware and Web3 OS ecosystem. Requires VERTU's blockchain infrastructure and is designed for their luxury smartphone platform only.
