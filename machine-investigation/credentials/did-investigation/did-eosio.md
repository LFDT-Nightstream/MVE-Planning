# did:eosio (EOSIO DID Method)

| Property | Value |
|----------|-------|
| Specification | [EOSIO DID Spec](https://github.com/Gimly-Blockchain/eosio-did-spec) |
| Organization | EOSIO Identity Working Group |
| DID Format | `did:eosio:<chain-name-or-id>:<account-name>` |

## Overview

The did:eosio method maps EOSIO account and permission structures to W3C DID Core. It supports multiple EOSIO-compatible blockchains and complex multi-signature authorization schemes.

Example DIDs:
- `did:eosio:telos:example`
- `did:eosio:4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11:example`

### DID Structure

- **Prefix**: `did:eosio:`
- **Chain identifier**: Registered name (telos, eos) or chain ID
- **Account name**: Human-readable (up to 13 characters)
- **Fragment** (optional): Permission reference (`#active`, `#owner`)

### Key Features

- **Permission hierarchy**: Native EOSIO multi-sig support
- **VerifiableCondition2021**: Complex threshold logic
- **Multi-chain**: Works across EOSIO chains
- **Human-readable accounts**: 13-char account names

### CRUD Operations

Operations vary by chain governance but follow standard patterns.

### Supported Key Types

- K1 (secp256k1)
- R1
- WA (P-256)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:eosio method requires:

- **EOSIO blockchain access**: Must query EOSIO chains
- **Multi-chain complexity**: Different chains have different governance
- **Permission system**: EOSIO-specific permission model
- **Account system**: Requires EOSIO account creation

**Adaptation possibility**: Very low. Deeply tied to EOSIO's account and permission model.

### 2. Ecosystem

**Active / EOSIO ecosystem**

- **Identity Working Group**: Community governance
- **Multiple contributors**: Gimly, Europechain, Block One
- **Multi-chain support**: EOS, Telos, Europechain
- **Apache-2.0 licensed**: Open development

### 3. Stability

**Active development**

- **Community maintained**: Working group model
- **Multi-stakeholder**: Several organizations involved
- **Production chains**: EOS, Telos are live

## Special Considerations

### Interesting Features

- **Permission mapping**: Native multi-sig to VerifiableCondition2021
- **Human-readable accounts**: User-friendly identifiers
- **Delegation logic**: Complex authorization schemes

### Limitations

- **Chain-specific governance**: Operations vary by chain
- **Account creation**: Requires resources/payment
- **EOSIO protocol v2.0+**: Version requirements

## Recommendation

**No-go**

The did:eosio method is unsuitable for our use case:

1. **EOSIO chain dependency**: Requires access to EOSIO chains
2. **Account system**: Tied to EOSIO account creation
3. **External state**: Cannot verify EOSIO state from our ledger
4. **Multi-chain complexity**: Different rules per chain

The permission mapping to VerifiableCondition2021 is interesting but doesn't justify the infrastructure requirements.
