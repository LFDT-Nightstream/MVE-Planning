# did:btco (Bitcoin Ordinals DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:btco Spec](https://identity.foundation/labs-ordinals-plus/btco-did-method/) |
| Organization | DIF Labs / Ordinals Plus |
| DID Format | `did:btco:<sat-number>` |

## Overview

The did:btco method leverages Bitcoin Ordinal Theory to create DIDs tied to specific satoshis. Each satoshi has a unique ordinal number, and the DID Document is inscribed directly on the satoshi. DID control follows UTXO ownership.

Example DID: `did:btco:1066296127976657`

### DID Structure

- **Prefix**: `did:btco:`
- **Sat-number**: Positive integer 0 to 2099999997689999 (satoshi ordinal)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Inscribe DID Document on unused satoshi with CBOR metadata |
| Read | Query satoshi inscription, decode CBOR, validate document |
| Update | Reinscribe on same satoshi with new document (requires UTXO control) |
| Deactivate | Reinscribe with `"deactivated": true` |

### Supported Algorithms

- **Ed25519**: Digital signatures (z6Mk prefix)
- **X25519**: Key agreement (z6LS prefix)
- **secp256k1**: Bitcoin compatibility (z6MW prefix)

Uses Multikey verification method type and Data Integrity proof suite.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:btco method requires Bitcoin blockchain and Ordinals infrastructure:

- **Bitcoin blockchain required**: All inscriptions on Bitcoin
- **Ordinals protocol dependency**: Requires `ord` client for inscription handling
- **UTXO control = DID control**: Must own the satoshi to modify DID
- **Full inscription history**: Resolution requires reading inscription chain

**Adaptation possibility**: Very low. The method is fundamentally tied to Bitcoin's UTXO model and the Ordinals inscription protocol. We cannot verify Bitcoin state or Ordinals inscriptions from our ledger.

### 2. Ecosystem

**Growing / DIF-backed**

- **DIF Labs incubation**: Backed by Decentralized Identity Foundation
- **Hackathon winner**: Won first place at inaugural Ordinals Hackathon
- **Active development**: Part of Ordinals Plus initiative
- **Bitcoin native**: Leverages Bitcoin's security model
- **Verifiable Credentials support**: Companion spec for VCs on Ordinals

### 3. Stability

**Early / Evolving**

- **Recent development**: Emerged from hackathon
- **Ordinals protocol dependency**: Tied to evolving Ordinals ecosystem
- **DIF backing**: Institutional support for continued development
- **Format changes**: Previous versions supported name/decimal formats, now ordinal-only

## Special Considerations

- **Satoshi ownership = control**: Bitcoin private key controls DID
- **Inscription immutability**: All inscriptions permanent on Bitcoin
- **Multi-key support**: Supports Ed25519, X25519, secp256k1
- **Data Integrity proofs**: Modern cryptographic proof format

## Recommendation

**No-go**

The did:btco method is unsuitable for our use case:

1. **Bitcoin dependency**: Requires Bitcoin blockchain and Ordinals protocol
2. **Cannot verify inscriptions**: Our ledger cannot read Bitcoin inscriptions
3. **UTXO complexity**: DID control tied to Bitcoin UTXO management

However, the design patterns are noteworthy:
- **Ordinal theory** for unique identifier assignment is clever
- **Inscription-based documents** provide immutability
- **UTXO-based control** is an interesting authorization model

If we ever need to reference Bitcoin-based DIDs, did:btco provides a well-designed approach, but we cannot natively support it.
