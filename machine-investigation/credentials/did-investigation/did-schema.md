# did:schema (Schema Registry)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/51nodes/schema-registry-did-method/blob/master/README.md) |
| Organization | 51nodes GmbH |
| DID Format | `did:schema:<storage-network>:<schema-type>:<content-hash>` |

## Overview
did:schema is a DID method for identifying schema definitions (JSON Schema, XSD) stored on IPFS. The content hash of the schema file becomes the DID identifier. Designed for decentralized schema management without blockchain.

## Evaluation

### 1. Feasibility/Complexity
**Not self-contained.** While it avoids blockchain, it requires IPFS infrastructure:

- Schemas stored on IPFS networks (public or evan.network cluster)
- Resolution verifies file existence and constructs DID document
- Service endpoints point to IPFS gateway URLs
- Updates create new DIDs (content-addressed immutability)
- Deletion not guaranteed across all IPFS nodes

No blockchain required, but IPFS network access is mandatory.

### 2. Ecosystem
**Small.** TypeScript implementation available (`@51nodes/decentralized-schema-registry`). Universal resolver integration exists. Focused on schema management use case.

### 3. Stability
**Moderate.** Clear, focused specification for a specific use case (schema identification). Reference implementation available.

## Recommendation
**No-go**

While did:schema avoids blockchain, it requires IPFS network access for storage and resolution. The dependency on distributed file storage makes it unsuitable for fully self-contained operation.
