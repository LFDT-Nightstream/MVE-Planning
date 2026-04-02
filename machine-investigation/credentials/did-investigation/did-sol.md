# did:sol (Solana)

| Property | Value |
|----------|-------|
| Specification | [Identity.com](https://g.identity.com/sol-did/) |
| Organization | Identity.com |
| DID Format | `did:sol[:network]:<40-48 base58 chars>` |

## Overview
did:sol is a DID method for storing and managing DID documents on the Solana blockchain. The identifier derives from the owner's x25519 public key combined with method-specific parameters through hashing. Supports mainnet, testnet, devnet, and localnet.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Solana blockchain:

- DID documents stored on-chain as Solana accounts
- Program ID: `idDa4XeCjVwKcprVAo812coUQbovSZ4kDGJf2sPaBnM`
- Resolution queries on-chain accounts, returns Borsh-serialized data
- If no on-chain data exists, a "generated" DID document is returned
- Requires client libraries for encoding/decoding

TypeScript reference implementation available.

### 2. Ecosystem
**Medium.** Part of Identity.com ecosystem on Solana. Benefits from Solana's broader blockchain adoption. Active development.

### 3. Stability
**Moderate.** Well-documented specification. Depends on Solana blockchain stability.

## Recommendation
**No-go**

Requires Solana blockchain for all DID operations. Cannot function without access to Solana network.
