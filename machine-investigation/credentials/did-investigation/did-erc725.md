# did:erc725 (ERC725 Ethereum Identity)

| Property | Value |
|----------|-------|
| Specification | [DID Method erc725](https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust-spring2018/blob/master/topics-and-advance-readings/DID-Method-erc725.md) |
| Organization | Web of Trust / Ethereum Community (Markus Sabadello, Fabian Vogelsteller, Peter Kolarov) |
| DID Format | `did:erc725:[network:]<40-hex-address>` |

## Overview

The did:erc725 method enables ERC725 smart contract identities on Ethereum to function as Decentralized Identifiers. ERC725 is a standard for blockchain-based identity proposed by Fabian Vogelsteller (who also created ERC20).

The DID format supports an optional network identifier (mainnet, ropsten, rinkeby, kovan) defaulting to mainnet. The method-specific identifier is a 40-character hexadecimal Ethereum address representing the ERC725 smart contract.

Example DIDs:
- `did:erc725:2F2B37C890824242Cb9B0FE5614fA2221B79901E`
- `did:erc725:ropsten:2F2B37C890824242Cb9B0FE5614fA2221B79901E`

Resolution involves querying the smart contract's `getKeysByType` function for MANAGEMENT, ACTION, CLAIM, and ENCRYPTION key types, then constructing a DID Document with the associated secp256k1 public keys.

## Evaluation

### 1. Feasibility/Complexity

**Not feasible without external dependencies.**

DID resolution requires:
1. Connecting to an Ethereum node (mainnet or testnets)
2. Querying the ERC725 smart contract at the specified address
3. Calling `getKeysByType` function to retrieve key material
4. Looking up raw public keys from native 20-byte addresses (acknowledged in spec as "complex and resource-intensive")

There is no way to verify or resolve a did:erc725 identifier without real-time access to Ethereum blockchain state. The DID itself contains only a contract address, not the cryptographic material needed for verification.

### 2. Ecosystem

**Limited adoption.**

- The ERC725 standard itself has evolved (ERC725X, ERC725Y) but did:erc725 as a DID method has not seen widespread adoption
- The specification dates from February 2018 (Rebooting Web of Trust Spring 2018)
- Some referenced testnets (Ropsten, Rinkeby, Kovan) have been deprecated
- No major DID resolver implementations actively maintain did:erc725 support
- Most Ethereum identity work has shifted to did:ethr which is more widely supported

### 3. Stability

**Incomplete and stale specification.**

- Last updated: February 2018
- Security and privacy sections marked as "TODO" (never completed)
- The specification acknowledges unresolved complexity around public key retrieval
- No evidence of continued development or maintenance
- Referenced test networks no longer exist

## Special Considerations

- The specification was a preliminary exploration from a Rebooting Web of Trust workshop
- ERC725 as a smart contract standard continues to evolve separately from this DID method
- The method requires gas fees for any updates (addKey, removeKey) or deletion (selfdestruct)
- Mixed-case checksum address encoding (EIP-55) is used for addresses
- Four key types are supported: MANAGEMENT, ACTION, CLAIM, and ENCRYPTION

## Recommendation

**No-go**

The did:erc725 method fundamentally requires Ethereum blockchain access for any DID resolution or verification. Since nodes cannot make HTTP requests or access external blockchain state, this method cannot be supported. Additionally, the specification is incomplete (with TODO sections for security/privacy), dates from 2018, references deprecated Ethereum testnets, and has been largely superseded by did:ethr in the Ethereum identity ecosystem. The combination of technical incompatibility with our constraints and the method's abandoned state makes this unsuitable for implementation.
