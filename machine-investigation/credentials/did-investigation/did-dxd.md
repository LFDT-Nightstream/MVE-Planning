# did:dxd (Data-Alliance DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:dxd Spec](https://github.com/Data-Alliance/did-dxd/blob/master/README.md) |
| Organization | Data-Alliance |
| DID Format | `did:dxd:<network-id><id-string><checksum>` |

## Overview

The did:dxd method is designed for secure authentication using DID and Verifiable Credentials. It uses a checksummed identifier derived from SHA3-256 hashing of public keys.

Example DID: `did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87`

### DID Structure

- **Prefix**: `did:dxd:`
- **Network ID**: 2 bytes (4 hex chars) - mainnet = "0000"
- **ID String**: 20 bytes (40 hex chars) from SHA3-256(public key)
- **Checksum**: 4 bytes (8 hex chars) from SHA3-256(network-id + id-string)

### Key Features

- **secp256k1**: Elliptic curve encryption
- **Base58 encoding**: For public keys
- **Verifiable Data Registry**: Central registry for DID documents
- **Service types**: DidControl, CitizenShip, IotLoRa, IotWifi

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Generate keys locally, submit enrollment to issuer, forward to registry |
| Read | Query registry using DID format |
| Update | Holder signatures for keys, issuer signatures for services |
| Deactivate | Via update operation |

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The did:dxd method requires:

- **Verifiable Data Registry**: Central registry for document storage
- **Issuer involvement**: Enrollment requires issuer participation
- **Service provider API**: Resolution through service providers

**Adaptation possibility**: Low. The system relies on a central registry and issuer infrastructure.

### 2. Ecosystem

**Limited**

- **Data-Alliance**: Single organization
- **W3C compliant**: Follows DID standards
- **Domain-specific**: IoT and citizenship focus
- **Unknown adoption**: Limited visibility

### 3. Stability

**Production**

- **Working implementations**: Operational system
- **W3C REC conformance**: Standards compliant

## Recommendation

**No-go**

The did:dxd method is unsuitable for our use case:

1. **Registry dependency**: Requires Verifiable Data Registry access
2. **Issuer involvement**: Not fully self-sovereign
3. **Service provider APIs**: Cannot self-verify
4. **Limited ecosystem**: Single vendor with specific use cases
