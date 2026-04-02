# did:antelope (Antelope Blockchain DID Method)

| Property | Value |
|----------|-------|
| Specification | [Antelope DID Spec](https://github.com/Tonomy-Foundation/antelope-did-spec) |
| Organization | Tonomy Foundation |
| DID Format | `did:antelope:<chain>:<account_name>` |

## Overview

The Antelope DID method enables decentralized identifiers on Antelope-based blockchains (EOS, Telos, WAX, etc.). It leverages Antelope's native account abstraction system with human-readable names and hierarchical permission structures.

Example DIDs:
- `did:antelope:telos:example`
- `did:antelope:eos:testnet:jungle:accountname`
- `did:antelope:4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11:example`

### DID Structure

Two schemas supported:
1. **Registered chain name**: `did:antelope:{chain_name}:{account_name}`
2. **Chain ID**: `did:antelope:{64-char-hex-chain-id}:{account_name}`

Account names: 1-13 characters (lowercase a-z, periods, digits 1-5)

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Smart contract dependent - varies by chain |
| Read | Query `get_account` API on Antelope nodes |
| Update | Update account permissions via on-chain transactions |
| Deactivate | Chain-specific implementation |

### Supported Algorithms

| Antelope Key Type | Verification Method | Curve |
|---|---|---|
| EOS_ (legacy) | EcdsaSecp256k1VerificationKey2019 | secp256k1 |
| PUB_K1_ | EcdsaSecp256k1VerificationKey2019 | secp256k1 |
| PUB_R1_ | JsonWebKey2020 | P-256 |
| PUB_WA_ (WebAuthn) | JsonWebKey2020 | P-256 |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:antelope method requires access to Antelope blockchain state:

- **Blockchain query required**: Resolution uses `get_account` API calls to Antelope nodes
- **Multi-chain complexity**: Supports many Antelope chains (EOS, Telos, WAX, etc.) with different configurations
- **Smart contract dependency**: Create/update/deactivate operations depend on chain-specific smart contracts
- **Permission hierarchy**: DID documents built from on-chain permission structures

**Adaptation possibility**: Low. While the account abstraction model is interesting (human-readable names, hierarchical permissions, weighted thresholds), it's fundamentally tied to querying Antelope blockchain state.

**Interesting aspects**: The permission hierarchy with weighted multi-sig and delegation could inspire our own design, but adopting did:antelope directly would require running Antelope nodes or trusting external APIs.

### 2. Ecosystem

**Moderate within Antelope ecosystem**

- **Multiple chains**: EOS, Telos, WAX, Proton, and other Antelope forks
- **Active development**: Tonomy Foundation actively maintains the spec
- **npm packages**: `@tonomy/antelope-did` and `@tonomy/antelope-did-resolver` available
- **Draft status**: Not yet a finalized standard
- **Enterprise focus**: Antelope designed for enterprise use cases

### 3. Stability

**Draft / Evolving**

- **Draft specification**: Not a W3C standard, actively evolving
- **Antelope 2.0+**: Targets modern Antelope versions
- **Dependency on draft standards**: Uses draft ConditionalProof2022 specification
- **Apache 2.0 license**: Open source

## Special Considerations

- **Human-readable names**: Account names are 1-13 character human-readable identifiers
- **Multi-chain support**: Works across all Antelope-based blockchains
- **Permission hierarchy**: Supports weighted thresholds and key delegation
- **WebAuthn support**: PUB_WA_ keys enable passkey/biometric authentication
- **Fragment navigation**: DID URLs can reference specific permissions (`#active`, `#owner`)

## Recommendation

**No-go**

The did:antelope method is unsuitable for our use case:

1. **Blockchain dependency**: Requires querying Antelope blockchain nodes for resolution
2. **Multi-chain complexity**: Would need to support multiple Antelope chains with different configurations
3. **Draft status**: Specification still evolving with dependencies on other draft standards

However, the design patterns are worth noting:
- Human-readable account names are user-friendly
- Hierarchical permissions with weighted thresholds are powerful for enterprise use
- WebAuthn key support enables modern authentication methods

These concepts could inform our own DID method design without adopting the Antelope-specific implementation.
