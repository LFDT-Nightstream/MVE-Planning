# did:erat (ERATOSTHENES DID Method)

| Property | Value |
|----------|-------|
| Specification | [did:erat Spec](https://gitlab.com/eratosthenes-h2020/erat-did-spec) |
| Organization | ERATOSTHENES H2020 Project (EU-funded consortium) |
| DID Format | `did:erat:<genesishash>:<identifier>` |

## Overview

The did:erat method was developed as part of the ERATOSTHENES H2020 project, a European Union-funded research initiative focused on creating a "decentralized and contextual Trust and Identity Management Framework" for IoT device lifecycle monitoring. The project ran from October 2021 to March 2025 with a budget of approximately 6 million EUR.

The method is designed specifically for resource-constrained IoT environments, enabling identity management for devices throughout their lifecycle. It uses Hyperledger Fabric (HLF) as its Verifiable Data Registry and relies on an Identity Agent (based on Hyperledger Aries) for DID lifecycle operations.

### DID Structure

- **Prefix**: `did:erat:`
- **Genesis Hash**: 64 hexadecimal characters (SHA-256 hash of the Hyperledger Fabric genesis block)
- **Identifier**: 64 hexadecimal characters (SHA-256 hash of concatenated nonce, genesis hash, and controller's public key)

Example format: `did:erat:a1b2c3...64chars...:d4e5f6...64chars...`

### CRUD Operations

| Operation | Method |
|-----------|--------|
| Create | Identity Agent submits controller data to Smart Contract, which stores DID Document in VDR |
| Read | Identity Agent identifies genesis hash, invokes `resolveDID()` Smart Contract function |
| Update | Agent verifies requester is DID controller, applies updates via Smart Contract |
| Deactivate | Removes public key from VDR-stored DID Document |

### Verification Methods

The specification uses Ed25519VerificationKey2018 with Base58-encoded public keys, supporting authentication and assertion methods.

### Resolution Process

1. Parse DID to extract genesis hash component
2. Identify the correct Hyperledger Fabric network from genesis hash
3. Invoke `resolveDID()` Smart Contract function on that network
4. Return the DID Document stored in the Verifiable Data Registry

## Evaluation

### 1. Feasibility/Complexity

**Not feasible - Requires blockchain access**

The did:erat method has fundamental dependencies that make it unsuitable for our use case:

- **Hyperledger Fabric dependency**: Resolution requires access to a specific HLF network identified by the genesis hash
- **Smart Contract invocation**: Reading a DID Document requires calling `resolveDID()` on the HLF network
- **Network connectivity required**: Nodes must connect to external HLF infrastructure to resolve DIDs
- **No deterministic resolution**: Unlike did:key, the DID Document cannot be derived from the identifier alone
- **Domain-specific**: The genesis hash ties DIDs to specific HLF deployments, requiring knowledge of multiple networks

There is no way to implement did:erat support without external network calls to Hyperledger Fabric infrastructure.

### 2. Ecosystem

**Limited / Research project**

- **Single implementation**: Developed solely within the ERATOSTHENES project
- **No Universal Resolver driver**: Not listed in the DIF Universal Resolver
- **Limited adoption**: Designed for specific IoT pilots (connected vehicles, smart health, industrial IoT)
- **Consortium-only**: The 15-organization EU consortium was the primary user base
- **No public tooling**: No widely available libraries or SDKs beyond project deliverables

### 3. Stability

**Research project - Now concluded**

- **Project ended**: ERATOSTHENES concluded in March 2025
- **Specification status**: Research output, not a formal standard
- **Limited maintenance**: With project completion, ongoing maintenance is uncertain
- **Not registered**: Not in the W3C DID Method Registry as a formal specification
- **Repository activity**: Minimal commits (7 total as of spec creation in October 2023)

## Special Considerations

### Privacy Features

- DID Documents exclude PII except for public key verification methods
- Random nonce in identifier prevents DID tracking across domains
- Supports "disposable identities" concept for enhanced privacy

### IoT Focus

- Designed specifically for resource-constrained IoT devices
- Integrates with ERATOSTHENES Trust Agents for continuous trust evaluation
- Supports device lifecycle management from provisioning to decommissioning

### EU Compliance

- Designed to support NIS Directive, GDPR, and EU Cybersecurity Act compliance
- Privacy-preserving attribute-based credentials (dp-ABC) integration

## Recommendation

**No-go**

The did:erat method is incompatible with our core requirement that nodes cannot make HTTP requests or access external blockchain state to validate DIDs.

Key blockers:

1. **Hyperledger Fabric dependency**: Every DID resolution requires querying a specific HLF network, which violates our constraint of no external network calls
2. **No self-describing identifiers**: Unlike did:key, the DID cannot be resolved without external infrastructure
3. **Limited ecosystem**: As a research project output with no broader adoption, there is minimal practical demand
4. **Maintenance uncertainty**: With the H2020 project concluded, the specification's future development is unclear
5. **IoT-specific design**: The method was purpose-built for IoT scenarios and does not generalize well

This method is architecturally incompatible with our use case and has insufficient ecosystem support to justify the complexity of any potential adaptation.
