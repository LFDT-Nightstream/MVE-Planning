# did:snplab (SNPLab)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/snplab-io/snplab-did-method/blob/main/snplab-did-method-spec.md) |
| Organization | SNPLab Inc. |
| DID Format | `did:snplab:<base58-ed25519-pubkey-prefix>` |

## Overview
did:snplab is a DID method using Hyperledger Fabric as its permissioned blockchain. The identifier is the base58 encoding of the first 16 bytes of the owner's Ed25519 public key. Uses libsodium for cryptographic operations.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Hyperledger Fabric infrastructure:

- Based on Hyperledger Fabric permissioned blockchain
- REST API endpoints for CRUD operations (`/account/document`)
- Ed25519 cryptography via libsodium
- TLS 1.2+ required for all communication
- Key rotation supported with proof-of-possession via nonce

### 2. Ecosystem
**Small.** Focused on SNPLab platform. Uses enterprise-grade infrastructure (Hyperledger Fabric).

### 3. Stability
**Moderate.** Well-documented specification following W3C standards. Enterprise blockchain foundation provides stability.

## Recommendation
**No-go**

Requires Hyperledger Fabric blockchain infrastructure and REST API access. Not suitable for self-contained operation.
