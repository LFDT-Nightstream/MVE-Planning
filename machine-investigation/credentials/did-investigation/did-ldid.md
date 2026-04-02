# did:ldid (ChainMaker / Longevity Digital)

| Property | Value |
|----------|-------|
| Specification | [GitHub](https://github.com/LONGEVITY-DIGITAL/LDID-Method-Specification/blob/main/README.md) |
| Organization | Longevity Digital |
| DID Format | `did:ldid:<base58_sha256_pubkey>` |

## Overview
LDID is a blockchain-based decentralized identifier method with a "local-first" privacy approach. The identifier is calculated as `base58(sha256(<Public Key PEM Format>))`. While DIDs can be generated locally, publication requires blockchain transactions for verification and discovery.

## Evaluation

### 1. Feasibility/Complexity
**Cannot work fully without external infrastructure.** The method offers:

**Local generation:** Users can generate DIDs locally, and sensitive information (private keys, personal data) stays local with only hashes uploaded to blockchain.

**Blockchain dependency:** Publication and verification require blockchain infrastructure for storing hash values and providing verifiable registration.

**Limited operations:** Currently only supports Create and Deactivate - Update operations are not yet implemented.

### 2. Ecosystem
Limited ecosystem information available. The specification mentions SM2 key type support, suggesting Chinese market focus (similar to did:kdid).

### 3. Stability
Early stage specification with limited functionality (no Update support). The local-first approach is privacy-positive but doesn't eliminate blockchain dependency for practical use.

## Recommendation
**No-go**

While local DID generation is possible, blockchain infrastructure is required for publication and verification. The incomplete specification (no Update operations) further limits practical applicability.
