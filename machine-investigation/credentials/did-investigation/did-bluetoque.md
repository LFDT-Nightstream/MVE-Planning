# did:bluetoque* (BlueToque Family - Agent, Deed, NFE, Process)

| Property | Value |
|----------|-------|
| Specification | [BlueToque Agent](https://github.com/mwherman2000/TrustedDigitalWeb/blob/master/specifications/did-methods/did-bluetoqueagent-1-0-0.md) |
| Organization | Hyperonomy Digital Identity Lab / Parallelspace Corporation |
| DID Formats | `did:bluetoqueagent:`, `did:bluetoquedeed:`, `did:bluetoquenfe:`, `did:bluetoqueproc:` |

## Overview

The BlueToque family consists of four related DID methods that are part of the "Trusted Digital Web" and "Fully Decentralized Objects Framework" initiatives. They share common architecture and are designed for different object types:

- **did:bluetoqueagent**: Decentralized agent identifiers
- **did:bluetoquedeed**: Deed/document identifiers
- **did:bluetoquenfe**: Non-fungible entity identifiers
- **did:bluetoqueproc**: Process identifiers

Example DID: `did:bluetoqueagent:interfaces:type:id-string:version`

### DID Structure

Two formats per method:
1. **Interfaces**: `did:bluetoqueagent:interfaces:[type]:[id-string][:version]`
2. **Implementation**: `did:bluetoqueagent:implementation:[type]:[id-string][:version]`

The id-string is derived using KERI key management techniques with Base64 encoding.

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | `RegIdWithPublicKey` - register with management key |
| Read | `GetDIDDocument` - retrieve DID document |
| Update | `AddKey` / `RemoveKey` - modify verification methods |
| Deactivate | Remove all public keys (prevents reactivation) |

### Supported Algorithms

- ECDSA (secp224r1, secp256r1, secp384r1, secp521r1)
- SM2 (sm2p256v1)
- EdDSA (ed25519)

## Evaluation

### 1. Feasibility/Complexity

**Not feasible for our use case**

The BlueToque methods have several issues:

- **Stratis blockchain required**: Uses Stratis Platform as Verifiable Data Registry
- **Complex framework**: Part of larger "Fully Decentralized Objects Framework"
- **KERI dependency**: Key derivation uses KERI techniques
- **Multiple related methods**: Four methods for different object types adds complexity

**Adaptation possibility**: Very low. The methods are part of a comprehensive framework with specific blockchain (Stratis) and architectural requirements. Would require adopting their entire ecosystem.

### 2. Ecosystem

**Very limited / Experimental**

- **Single organization**: Hyperonomy Digital Identity Lab
- **Research project**: Appears to be more research/experimental than production
- **No Universal Resolver support**: Not integrated with standard tooling
- **Limited documentation**: Specifications available but minimal adoption evidence
- **Stratis platform**: Small blockchain ecosystem

### 3. Stability

**Experimental / Uncertain**

- **Version 1.0.0**: Initial specification
- **Research-oriented**: Part of academic/research framework
- **Single maintainer**: Individual developer (mwherman2000)
- **Limited activity**: Minimal recent updates visible

## Special Considerations

- **KERI integration**: Uses KERI for key management
- **Framework approach**: Designed as part of comprehensive decentralized object system
- **Multiple object types**: Agent, deed, NFE, process - suggests enterprise workflow focus
- **Permanent deactivation**: Removed keys cannot be re-added

## Recommendation

**No-go**

The BlueToque DID methods are unsuitable for our use case:

1. **Stratis blockchain dependency**: Requires specific blockchain infrastructure
2. **Experimental status**: Research project with minimal production adoption
3. **Complex framework**: Part of larger system we would need to adopt
4. **Limited ecosystem**: Single organization, no broad tooling support

The KERI integration is interesting, but if we want KERI-based key management, we should look at dedicated KERI DID methods (did:keri) rather than this framework-specific implementation.
