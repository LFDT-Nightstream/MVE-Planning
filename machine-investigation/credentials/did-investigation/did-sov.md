# did:sov (Sovrin)

| Property | Value |
|----------|-------|
| Specification | [Sovrin Foundation](https://sovrin-foundation.github.io/sovrin/spec/did-method-spec-template.html) |
| Organization | Sovrin Foundation / Hyperledger Indy |
| DID Format | `did:sov:<21-22 base58 chars>` |

## Overview
did:sov is the DID method for the Sovrin network, a public permissioned blockchain designed specifically for privacy-preserving self-sovereign identity. It was one of the earliest DID methods and has been operational since July 2017. DIDs are derived from 16-byte UUIDs encoded in base58.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** The method fundamentally requires the Sovrin/Hyperledger Indy ledger for all operations:

- **Create**: Requires Trust Anchor role to submit `NYM` transactions
- **Read**: Uses `GET_NYM` and `GET_ATTRIB` transactions to retrieve DID records
- **Update**: Authenticated transactions to modify ledger records
- **Delete**: Setting document field to null permanently terminates the identity

The resolver constructs DID Documents by querying the ledger and deriving X25519 keys from stored ED25519 keys.

### 2. Ecosystem
**Large but declining.** Sovrin was a pioneer in the SSI space with significant enterprise adoption. However, the network has faced sustainability challenges. The broader Hyperledger Indy ecosystem remains active with deployments in government and enterprise contexts.

### 3. Stability
**Mature specification.** The did:sov method is one of the most thoroughly documented DID methods. It has been in production since 2017 and follows W3C DID standards closely.

## Recommendation
**No-go**

did:sov requires the Sovrin/Indy blockchain infrastructure for all operations. There is no way to resolve or verify DIDs without access to the Sovrin ledger. This is a fundamental architectural dependency that cannot be avoided.
