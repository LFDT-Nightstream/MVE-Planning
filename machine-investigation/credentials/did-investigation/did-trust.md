# did:trust (TrustCerts)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/trustcerts/did-trust-method/blob/main/README.md) |
| Organization | TrustCerts GmbH |
| DID Format | `did:trust:<namespace>:<identifier>` |

## Overview
did:trust is a transaction-based DID method built on the Trustchain blockchain. Rather than storing complete documents, it composes them from chronological transactions, enabling built-in versioning and audit trails. Supports multiple namespace types (id, sch, tmp, hash).

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method requires Trustchain infrastructure:

- Distributed network with Gateways (write), Observers (read), Validators (consensus)
- DID documents assembled from chronological blockchain transactions
- Resolver must register endpoints per namespace
- Chain-of-trust verification back to genesis block
- didDocSignature values for cryptographic verification

Two resolution modes: transaction-based (full history) or document-based (pre-parsed).

### 2. Ecosystem
**Small.** Focused on TrustCerts platform. Enterprise-oriented design with governance features.

### 3. Stability
**Moderate.** Well-documented specification with sophisticated transaction-based design. Depends on Trustchain network stability.

## Recommendation
**No-go**

Requires Trustchain blockchain network with multiple node types (Gateways, Observers, Validators). Not suitable for self-contained operation.
