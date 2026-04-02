# did:yourd (YourD)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/DataSovereignty-YourD/YourD-did-sepcification) |
| Organization | DataSovereignty-YourD |
| DID Format | `did:yourd:tezos:mainnet:tz1cYSgk4T76D87d5tDQnmXTDo6mCXJgKVQe` |

## Overview
did:yourd is a DID method supporting multiple blockchains (Aleo, Klaytn, Tezos) with a hybrid architecture. The DID format includes blockchain and network identifiers. DID documents are stored locally in user-controlled storage (mobile/PC apps), encrypted with AES, while only metadata is registered on-chain for integrity verification. The method emphasizes privacy, GDPR compliance, and data sovereignty.

## Evaluation

### 1. Feasibility/Complexity
**Not feasible for ledger-based registry.** Requires blockchain infrastructure plus local storage:
- Blockchain-specific format (must specify chain and network)
- Currently supports: Aleo, Klaytn, Tezos
- Hybrid storage: documents local (AES encrypted), metadata on-chain
- Biometric authentication for local access
- Zero-knowledge proofs for credential submission
- Resolution combines local and on-chain data

### 2. Ecosystem
Open-source project (DataSovereignty-YourD). Multi-chain support provides some flexibility. Focus on privacy and GDPR compliance suggests European market targeting. Uses QR codes for credential issuance flows.

### 3. Stability
GitHub-based specification. The multi-chain approach adds complexity but provides chain flexibility. Privacy-focused design with ZKP support is architecturally interesting.

## Recommendation
**No-go**

Requires one of the supported blockchains (Aleo, Klaytn, or Tezos) plus local application storage. The hybrid local+blockchain model cannot function as a self-contained system.
