# did:meme (IPFS + Steganography)

| Property | Value |
|----------|-------|
| Specification | [GitHub Pages](https://or13.github.io/didme.me/did-method-spec) |
| Organization | Open Source (or13) |
| DID Format | `did:meme:<bech32_encoded_multihash>` |

## Overview
did:meme is an experimental DID method that combines IPFS content addressing with image steganography. A public key is embedded inside a "meme" image using steganographic techniques, the image is uploaded to IPFS, and the resulting CID is bech32-encoded to form the DID. Resolution involves retrieving the image and extracting the embedded key material.

## Evaluation

### 1. Feasibility/Complexity
**Requires IPFS infrastructure with significant limitations.** The resolution process:
1. Convert bech32 identifier to IPFS CID
2. Retrieve image from IPFS
3. Extract steganographically embedded key material
4. Reconstruct DID document

**Critical limitations:**
- Only Create and Read operations supported
- Update and Deactivate explicitly unsupported
- Intended for "short-lived interactions" due to security limitations
- Multiple DIDs can map to the same underlying key (vulnerability)

### 2. Ecosystem
Experimental/novelty project. The "meme" concept is creative but not suitable for production identity systems. Limited to specific use cases where immutability and short-lived identity are acceptable.

### 3. Stability
**Not production-ready.** The specification acknowledges security vulnerabilities (multiple DIDs mapping to same key post-compromise). The lack of update/deactivate operations makes key rotation impossible.

## Recommendation
**No-go**

Experimental method with acknowledged security limitations. Requires IPFS infrastructure and cannot support key rotation or deactivation. Not suitable for production identity use cases.
