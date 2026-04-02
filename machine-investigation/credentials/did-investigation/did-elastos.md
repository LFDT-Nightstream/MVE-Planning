# did:elastos (Elastos DID Method)

| Property | Value |
|----------|-------|
| Specification | [Elastos DID Spec](https://github.com/elastos/Elastos.DID.Method/blob/master/DID/Elastos-DID-Method-Specification_en.md) |
| Organization | Elastos Foundation |
| DID Format | `did:elastos:<base58-id>` |

## Overview

The did:elastos method uses the Elastos ID Sidechain as its distributed ledger. DIDs are managed through transactions on this sidechain, with the ID string starting with 'i' in Bitcoin-style Base58 encoding.

Example DID: `did:elastos:icJ4z2DULrHEzYSvjKNJpKyhqFDxvYV7pN`

### DID Structure

- **Prefix**: `did:elastos:` (can be omitted within Elastos systems)
- **Identifier**: Base58 encoded, starts with 'i'
- **Paths/fragments**: Optional extensions supported

### Key Features

- **JSON (not JSON-LD)**: Simplified document format
- **Mandatory expiration**: Max 5 years validity
- **Delegation**: Authorization for deactivation to trusted parties
- **Embedded credentials**: Public VCs in document

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate keys, publish transaction to ID Sidechain |
| Read | Retrieve from blockchain, validate proofs |
| Update | Submit new transaction with proof signature |
| Deactivate | Subject or delegated party can deactivate |

### Supported Algorithms

- ECDSAsecp256r1 (default)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:elastos method requires:

- **Elastos ID Sidechain**: All operations require sidechain access
- **DID client**: Transactions via DID client software
- **Sidechain transactions**: Requires Elastos tokens
- **Blockchain validation**: Proofs validated against chain

**Adaptation possibility**: Low. While the design patterns are good (delegation, expiration), the sidechain dependency is a blocker.

### 2. Ecosystem

**Active / Elastos ecosystem**

- **Elastos Foundation**: Active organization
- **Production use**: Live sidechain
- **SDK available**: Developer tools
- **W3C mapping**: Interoperability planned

### 3. Stability

**Mature (v0.2)**

- **November 2019**: Well-established
- **Foundation backing**: Institutional support
- **Production deployment**: Live system

## Special Considerations

### Good Design Patterns

- **Mandatory expiration**: Max 5 years, prevents indefinite validity
- **Delegation mechanism**: Key recovery via trusted parties
- **Simplified JSON**: Developer-friendly format
- **Proof signatures**: Integrity protection

### Limitations

- **Sidechain dependency**: Cannot operate independently
- **ECDSAsecp256r1**: Different curve than typical (secp256k1)

## Recommendation

**No-go**

The did:elastos method is unsuitable for our use case:

1. **Elastos sidechain dependency**: Requires sidechain access
2. **Token requirements**: Transactions need Elastos tokens
3. **External chain state**: Cannot verify from our ledger
4. **secp256r1**: Different curve than most DID methods

The design patterns (expiration, delegation) are worth noting but the infrastructure requirements are prohibitive.
