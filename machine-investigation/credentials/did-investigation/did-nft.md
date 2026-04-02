# did:nft (NFT DID Method)

| Property | Value |
|----------|-------|
| Specification | [CIP-94](https://cips.ceramic.network/CIPs/cip-94) |
| Organization | Ceramic Network |
| DID Format | `did:nft:<CAIP-19 Asset ID with / replaced by _>` |

## Overview

The NFT DID Method converts any non-fungible token (NFT) on any blockchain into a decentralized identifier where the **owner of the NFT is the controller of the DID**. This enables ownership-based access control where the controller of a DID automatically changes when the NFT is transferred.

The method works by:
1. Encoding the NFT using CAIP-19 (Chain Agnostic Improvement Proposal) Asset IDs
2. Looking up the current owner on the blockchain
3. Converting the owner's account to a CAIP-10 Account ID
4. Resolving the owner's DID via Ceramic Network's `caip10-link` document

Example DID format:
```
did:nft:eip155:1_erc721:0x06012c8cf97BEaD5deAe237070F9587f8E7A266d_771769
```

This represents an ERC-721 token on Ethereum mainnet (chain ID 1) at the specified contract address with token ID 771769.

## Evaluation

### 1. Feasibility/Complexity

**Cannot support without external dependencies.**

Resolution of did:nft requires multiple external dependencies:
- **Blockchain access**: Must query the NFT's smart contract to determine current ownership
- **The Graph Protocol**: Required for querying blockchain data (ERC721/ERC1155 subgraphs)
- **Ceramic Network**: Must access caip10-link documents to resolve the owner's blockchain account to their DID
- **Multiple subgraph endpoints**: Per network, requires blocks, ERC721, and ERC1155 subgraph endpoints

The verification method is `BlockchainVerificationMethod2021`, which ties verification to the blockchain account of the current NFT owner, requiring live blockchain state access.

**Verdict: Not feasible for offline verification. Nodes cannot verify without HTTP calls and blockchain access.**

### 2. Ecosystem

**Limited adoption and experimental status.**

- GitHub repository: [ceramicnetwork/nft-did-resolver](https://github.com/ceramicnetwork/nft-did-resolver)
- 32 stars, 10 forks, 6 contributors
- Only TypeScript implementation available
- Officially described as "still a prototype" and "very experimental"
- Limited to ERC-721 and ERC-1155 tokens
- Only Ethereum mainnet, Rinkeby (deprecated), and Polygon supported by default
- Listed in W3C DID Method Registry

### 3. Stability

**Draft specification with minimal maintenance.**

- Specification status: **Draft** (since February 12, 2021)
- Last significant repository update: March 2023
- No updates for nearly 3 years
- Ceramic Network has shifted focus to other identity solutions
- Known security consideration: Current holder must trust all previous holders not to override content of Ceramic streams controlled by the did:nft

## Special Considerations

- **Dynamic ownership**: The DID controller changes automatically when the NFT is transferred, which is unique but creates verification complexity
- **Historical resolution**: Supports `versionTime` parameter for resolving ownership at specific points in time (requires The Graph)
- **Privacy limitation**: Encrypted content does not automatically transfer to new owners
- **Trust issue**: Previous NFT holders could potentially override stream content
- **Chain limitations**: Only supports specific chains and token standards (ERC-721/ERC-1155)

## Recommendation

**No-go**

The did:nft method is fundamentally incompatible with our key constraint that nodes cannot make HTTP requests to validate DIDs. Resolution requires:

1. Live blockchain queries to determine NFT ownership
2. Access to The Graph Protocol subgraphs for blockchain data
3. Access to Ceramic Network to resolve owner accounts to DIDs

Even if we could cache some data, the dynamic nature of NFT ownership (which can change with any transfer) means that verification inherently requires real-time blockchain state. There is no way to verify a did:nft identifier without external network calls.

Additionally, the specification remains in draft status with minimal maintenance since 2023, the implementation is explicitly labeled as experimental, and the ecosystem adoption is very limited.
