# DID Goals

This document outlines some of the goals we would need from DIDs (Decentralized Identifiers) as per the [W3C DID spec](https://www.w3.org/TR/did-1.1/).

The end goal is to allow developers to manage (create, update) metadata associated with their smart contract on a ledger.

## Flow

There are two common flows we would like to support:
1. (simple case) contract has a single owner with a single key they manage
2. (enterprise case) contract is owned by an entity that may have multiple employees, and therefore has to manage issuing, revoking and updating of credentials

The process itself looks like the following (the flow is the same for enterprises and individuals):
1. The enterprise registers a DID
2. Verifiable Credentials (VCs) are issues to the employees (and stored in their wallets) to encode the fact they have permission to update contract metadata
3. Employees, when they want to update metadata, issues a Verifiable Presentation (VP) to prove they have permission to make the update

## Goals & Requirements

In this proof of concept, we do not concern ourselves with
- *what* the metadata is - our only goal is to allow it to be updatable
- *how* contracts are actually represented. We just use an immutable mapping of `DID -> random contract ID -> metadata`

Conceptually, the functionality we want to support are:
- `createDID`
- `revokeDID`
- `updateDID`
- `uploadContract` (note: cannot delete or update contracts)
- `updateMetadata`

Additionally,
- Nodes cannot make HTTP requests to validate DIDs (but can be done on client-side, with information from a URL resolved and passed to the nodes. Note that this requires a proof that the data really did come from the location)
- No need to persist old (overwritten) metadata
- DID revocation does not delete the contract from the registry, but does delete the metadata.
- One DID can manage multiple contracts

Some specific user-flows we would like to support in an ideal world:
- User deploys contracts through oauth
- User deploys contracts through passkeys (or other trusted hardware modules)
- User deploys through air-gapped keys (ex: hardware wallets)
- User deploys through their crypto wallet
- User deploys through their identity wallet

Importantly, our most important goal is to invent as few things as possible! Wherever there is an existing standard we can use, we should use this. This maximizes the number of tools that support our system today, and maximizes the chance of many tools supporting our system in the future. It's important to recognize that we are not the first DID project - we are not the second DID project - we are not even the 100th DID project! Almost anything we could think of, likely some other project has thought about it and it either is mentioned in a spec or at the very least exists in some implementation.

## DID Methods

DIDs are just an id. What the DID actually encodes, and what you can do with it, is called the DID *method*. While there are many [known DID methods](https://www.w3.org/TR/did-extensions-methods/), we cannot support all of them.

As a brief intuition for why, we discuss three canonical examples:
1. [did:key](https://w3c-ccg.github.io/did-key-spec/) encodes the public key directly into the DID (ex: `did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK`). However, the simplicity also means that key updates (revocation and rotation) are not supported. While this means that are not well suited for enterprise use-cases, the simplicity makes it easy to support (for some selection of cryptographic key types).
2. [did:web](https://w3c-ccg.github.io/did-method-web/) resolves the DID via DNS. While simple, we cannot (from the ledger) prove that the data really does from that DNS (unless we also support more complex protocols like zkTLS).
3. [did:ethr](https://github.com/decentralized-identity/ethr-did-resolver/blob/b02a6a43afa1e102e50db664f7cf233677ec07e9/doc/did-method-spec.md) encodes the DID as a smart contract on an EVM blockchain, meaning updates to the DID are performed as updates to the contract state. That means that, unless our ledger itself supports EVM contracts (and can inspect its own state), we cannot support this DID method in a trustless manner as it would require at the very least a trustless proof of the entire state of Ethereum to validate the current state of the DID (complicated further by the fact that there is no guarantee the DID comes from Ethereum and not one of the many other EVM chains). Therefore, these kinds of chain-based DID methods are not supported.

This means we have to limit ourselves to DID methods where node operators can, without any external data (or query) beyond what the user provides, verify the correctness of the information. Naturally, this is complex for DID methods that support stateful operations like key rotations.

### Easily Supported DID methods

The following DID methods are easy to support, and are extremely common:
- `did:key` - simple to support as it is just a key
- `did:jwk` - as powerful as `did:key`, but in a `jwk` interface which is a format expected by many tools

### Method Investigation

Our goal is to investigate *every* known DID method [registered with the W3C](https://www.w3.org/TR/did-extensions-methods/) to see if they are usable for our project

Note the investigation is more complex than it seems. For example,
- it is tempting to dismiss standards like `did:ion` because it is made for Bitcoin (and we cannot easily know the state of the Bitcoin blockchain). However, a lot of these systems are actually ledger-agnostic under the hood (ex: just require being able to read & write to *some* immutable ledger). These means that for some ledger-based VDRs, we can actually use them by just providing a different driver that connects to our registry instead (note: this is not possible for ALL ledger-based VDRs, nor does it necessarily mean it's a good idea. It means we need to evaluate on a case-by-case basis)
- it is tempting to dismiss centralized standards, but the node could be the "centralized" registry in a sense (of course, the ledger is not actually centralized, but many schemes should still work as-is regardless! Just because they were designed with a centralized server in mind, doesn't mean we have to dismiss it. It has to be a case-by-case)
- it is tempting to dismiss solutions that require totally different network topologies, the existence of cryptographic primitives, or other things that may seem out of reach. We are the ones writing the ledger, so we can add whatever is required if it makes sense!

Given this, we should think deeply about DID methods based on
1. Feasibility/Complexity (ex: `did:key` is easy. If it's not easily feasible, why?)
2. Ecosystem (are there many tools for this DID method? Or is it unpopular? Deprecated?)
3. Stability (ex: is the standard recently updated? While actively maintained standards are good, it also means there could be breaking changes coming)

For each DID method, we should evaluate them based on the above-mentioned points, but also include any special considerations that some DID methods may have. Then, we should also give it a final recommendation with four possibilities:
1. Recommended (we should support this DID method, or standards from this DID method including partial support)
2. Interesting (not recommended for integration, but has some ideas that are worth keeping in context)
3. Requires investigation (you need extra context from to make a decision)
4. No-go

#### Investigation result

Each method is investigated by AI as a different markdown files. Here is a table that summarizes the result of each DID method, and provides a link to the markdown with the full investigation for that specific version of the specification.

| Method | Recommendation | Notes |
|--------|----------------|-------|
| [did:jwk](../../machine-investigation/credentials/did-investigation/did-jwk.md) | **Recommended** | Self-contained; JWK format familiar to enterprise |
| [did:keri](../../machine-investigation/credentials/did-investigation/did-keri.md) | **Recommended** | Self-certifying; truly ledger-agnostic; strong security |
| [did:key](../../machine-investigation/credentials/did-investigation/did-key.md) | **Recommended** | Self-contained; deterministic; wide ecosystem; no external deps |
| [did:ni](../../machine-investigation/credentials/did-investigation/did-ni.md) | **Recommended** | Self-certifying; hash-based; fully offline verification |
| [did:nostr](../../machine-investigation/credentials/did-investigation/did-nostr.md) | **Recommended** | Self-certifying; secp256k1 key in DID; offline-first |
| [did:pkh](../../machine-investigation/credentials/did-investigation/did-pkh.md) | **Recommended** | Self-certifying from blockchain address; no lookups |
| [did:self](../../machine-investigation/credentials/did-investigation/did-self.md) | **Recommended** | Self-certified; no external infrastructure; JWK-based |
| [did:asset](../../machine-investigation/credentials/did-investigation/did-asset.md) | Interesting | Asset identification only; multi-chain dependency; read-only |
| [did:ccf](../../machine-investigation/credentials/did-investigation/did-ccf.md) | Interesting | Requires CCF + TEE hardware; Microsoft/Azure ecosystem |
| [did:cheqd](../../machine-investigation/credentials/did-investigation/did-cheqd.md) | Requires investigation | Cosmos-based; good design patterns for versioning |
| [did:dyne](../../machine-investigation/credentials/did-investigation/did-dyne.md) | Requires investigation | Zenroom crypto VM; spec not fully accessible |
| [did:hpo](../../machine-investigation/credentials/did-investigation/did-hpo.md) | Requires investigation | Bitcoin-style keys; local storage; immutable |
| [did:iden3](../../machine-investigation/credentials/did-investigation/did-iden3.md) | Requires investigation | ZKP architecture; interesting patterns |
| [did:indy](../../machine-investigation/credentials/did-investigation/did-indy.md) | Requires investigation | Hyperledger; self-certifying DIDs; VC patterns |
| [did:infra](../../machine-investigation/credentials/did-investigation/did-infra.md) | Requires investigation | Public-key DIDs need no registration until modified |
| [did:ion](../../machine-investigation/credentials/did-investigation/did-ion.md) | Requires investigation | Sidetree is ledger-agnostic; could use our ledger as anchor |
| [did:kilt](../../machine-investigation/credentials/did-investigation/did-kilt.md) | Requires investigation | Light DIDs are self-contained; limited features |
| [did:life](../../machine-investigation/credentials/did-investigation/did-life.md) | Requires investigation | Self-describing identifier; blockchain for resolution |
| [did:mesh](../../machine-investigation/credentials/did-investigation/did-mesh.md) | Requires investigation | KERI-based; distributed mesh nodes |
| [did:peer](../../machine-investigation/credentials/did-investigation/did-peer.md) | Requires investigation | Numalgo 0/2 feasible; designed for pairwise, not public registry |
| [did:prism](../../machine-investigation/credentials/did-investigation/did-prism.md) | Requires investigation | Cardano-based; excellent design patterns for key mgmt |
| [did:tz](../../machine-investigation/credentials/did-investigation/did-tz.md) | Requires investigation | Multi-modal; implicit mode like did:key |
| [did:webs](../../machine-investigation/credentials/did-investigation/did-webs.md) | Requires investigation | did:web + KERI; self-certifying components |
| [did:webvh](../../machine-investigation/credentials/did-investigation/did-webvh.md) | Requires investigation | Verifiable history; DIF-backed |
| [did:3](../../machine-investigation/credentials/did-investigation/did-3.md) | No-go | Tightly coupled to Ceramic Network; declining ecosystem |
| [did:abt](../../machine-investigation/credentials/did-investigation/did-abt.md) | No-go | Single-vendor (ArcBlock); requires ABT blockchain; incomplete spec |
| [did:aergo](../../machine-investigation/credentials/did-investigation/did-aergo.md) | No-go | Requires Aergo blockchain; limited ecosystem |
| [did:ala](../../machine-investigation/credentials/did-investigation/did-ala.md) | No-go | Requires Alastria consortium membership; regional (Spain) focus |
| [did:amo](../../machine-investigation/credentials/did-investigation/did-amo.md) | No-go | Requires AMO blockchain; niche automotive focus; incomplete spec |
| [did:andorra](../../machine-investigation/credentials/did-investigation/did-andorra.md) | No-go | Government-controlled; read-only; Andorra jurisdiction only |
| [did:antelope](../../machine-investigation/credentials/did-investigation/did-antelope.md) | No-go | Requires Antelope blockchain nodes; multi-chain complexity |
| [did:art](../../machine-investigation/credentials/did-investigation/did-art.md) | No-go | Centralized resolver; art industry specific; Enecuum blockchain |
| [did:bba](../../machine-investigation/credentials/did-investigation/did-bba.md) | No-go | Requires Ardor blockchain; small ecosystem |
| [did:bee](../../machine-investigation/credentials/did-investigation/did-bee.md) | No-go | Centralized platform (mesur.io); OAuth gated; domain-specific |
| [did:bid](../../machine-investigation/credentials/did-investigation/did-bid.md) | No-go | Requires BIF blockchain; Chinese market focus |
| [did:bit](../../machine-investigation/credentials/did-investigation/did-bit.md) | No-go | Requires Nervos CKB; .bit platform dependency |
| [did:bluetoque*](../../machine-investigation/credentials/did-investigation/did-bluetoque.md) | No-go | Requires Stratis blockchain; experimental framework |
| [did:bnb](../../machine-investigation/credentials/did-investigation/did-bnb.md) | No-go | Requires BNB Smart Chain; same issues as did:ethr |
| [did:bryk](../../machine-investigation/credentials/did-investigation/did-bryk.md) | No-go | Centralized default; custom protocols; limited ecosystem |
| [did:bsv](../../machine-investigation/credentials/did-investigation/did-bsv.md) | No-go | Requires Bitcoin SV; controversial ecosystem |
| [did:btco](../../machine-investigation/credentials/did-investigation/did-btco.md) | No-go | Requires Bitcoin + Ordinals protocol |
| [did:btcr](../../machine-investigation/credentials/did-investigation/did-btcr.md) | No-go | Requires Bitcoin; superseded by newer methods |
| [did:candid](../../machine-investigation/credentials/did-investigation/did-candid.md) | No-go | Specification not available (404); abandoned |
| [did:ccd](../../machine-investigation/credentials/did-investigation/did-ccd.md) | No-go | Requires Concordium blockchain; chain-specific |
| [did:ccp](../../machine-investigation/credentials/did-investigation/did-ccp.md) | No-go | Baidu vendor-locked; requires Baidu infrastructure |
| [did:celo](../../machine-investigation/credentials/did-investigation/did-celo.md) | No-go | Requires Celo blockchain; same issues as did:ethr |
| [did:ckb](../../machine-investigation/credentials/did-investigation/did-ckb.md) | No-go | Requires Nervos CKB blockchain; Cell model specific |
| [did:cn](../../machine-investigation/credentials/did-investigation/did-cn.md) | No-go | Chinese government-controlled; not self-sovereign |
| [did:cndid](../../machine-investigation/credentials/did-investigation/did-cndid.md) | No-go | Requires specific blockchain; Chinese market focus |
| [did:com](../../machine-investigation/credentials/did-investigation/did-com.md) | No-go | Requires Commercio.network blockchain |
| [did:content](../../machine-investigation/credentials/did-investigation/did-content.md) | No-go | Content identification only; not for entity identity |
| [did:corda](../../machine-investigation/credentials/did-investigation/did-corda.md) | No-go | Requires Corda network; enterprise permissioned ledger |
| [did:cosmos](../../machine-investigation/credentials/did-investigation/did-cosmos.md) | No-go | Requires Cosmos SDK chains; multi-chain dependency |
| [did:cot](../../machine-investigation/credentials/did-investigation/did-cot.md) | No-go | Requires CoTNetwork; phone verification dependency |
| [did:cr](../../machine-investigation/credentials/did-investigation/did-cr.md) | No-go | Copyright management specific; different use case |
| [did:ct](../../machine-investigation/credentials/did-investigation/did-ct.md) | No-go | Requires CircularTrust/IDEN3; API key access |
| [did:ctid](../../machine-investigation/credentials/did-investigation/did-ctid.md) | No-go | Chinese cryptography (SM2/SM3); institutional access only |
| [did:dht](../../machine-investigation/credentials/did-investigation/did-dht.md) | No-go | Requires Mainline DHT infrastructure |
| [did:did](../../machine-investigation/credentials/did-investigation/did-did.md) | No-go | Specification not accessible; abandoned |
| [did:dime](../../machine-investigation/credentials/did-investigation/did-dime.md) | No-go | Requires Dimecoin blockchain; minimal ecosystem |
| [did:dns](../../machine-investigation/credentials/did-investigation/did-dns.md) | No-go | Requires DNS resolution; same issue as did:web |
| [did:dock](../../machine-investigation/credentials/did-investigation/did-dock.md) | No-go | Requires Dock blockchain; Substrate ecosystem |
| [did:dom](../../machine-investigation/credentials/did-investigation/did-dom.md) | No-go | No specification; Ethereum-based |
| [did:dotbit](../../machine-investigation/credentials/did-investigation/did-dotbit.md) | No-go | Requires Nervos/.bit naming system |
| [did:drop](../../machine-investigation/credentials/did-investigation/did-drop.md) | No-go | Drone delivery specific; Algorand dependency |
| [did:dsrv](../../machine-investigation/credentials/did-investigation/did-dsrv.md) | No-go | Cosmos dependency for revocation; no key rotation |
| [did:dual](../../machine-investigation/credentials/did-investigation/did-dual.md) | No-go | EVM dependency; same issues as did:ethr |
| [did:dweb](../../machine-investigation/credentials/did-investigation/did-dweb.md) | No-go | Aisino blockchain; no updates; minimal ecosystem |
| [did:dxd](../../machine-investigation/credentials/did-investigation/did-dxd.md) | No-go | Requires central registry; issuer involvement |
| [did:echo](../../machine-investigation/credentials/did-investigation/did-echo.md) | No-go | Requires ECHO protocol; incomplete spec |
| [did:elastos](../../machine-investigation/credentials/did-investigation/did-elastos.md) | No-go | Requires Elastos sidechain; good design patterns |
| [did:elem](../../machine-investigation/credentials/did-investigation/did-elem.md) | No-go | Archived project (2021); superseded by did:ion |
| [did:emtrust](../../machine-investigation/credentials/did-investigation/did-emtrust.md) | No-go | Hyperledger Fabric; permissioned model |
| [did:ens](../../machine-investigation/credentials/did-investigation/did-ens.md) | No-go | Requires Ethereum/ENS; same as did:ethr |
| [did:eosio](../../machine-investigation/credentials/did-investigation/did-eosio.md) | No-go | Requires EOSIO blockchain access |
| [did:erat](../../machine-investigation/credentials/did-investigation/did-erat.md) | No-go | Requires Hyperledger Fabric blockchain access |
| [did:erc725](../../machine-investigation/credentials/did-investigation/did-erc725.md) | No-go | Requires Ethereum blockchain; abandoned since 2018 |
| [did:etho](../../machine-investigation/credentials/did-investigation/did-etho.md) | No-go | Same as did:ethr; Ethereum dependency |
| [did:ethr](../../machine-investigation/credentials/did-investigation/did-ethr.md) | No-go | Requires Ethereum; same fundamental issue as all EVM methods |
| [did:ev](../../machine-investigation/credentials/did-investigation/did-ev.md) | No-go | Requires Ethereum blockchain for resolution |
| [did:evan](../../machine-investigation/credentials/did-investigation/did-evan.md) | No-go | Requires evan.network; enterprise/IoT focus |
| [did:everscale](../../machine-investigation/credentials/did-investigation/did-everscale.md) | No-go | Spec not accessible; Everscale dependency |
| [did:example](../../machine-investigation/credentials/did-investigation/did-example.md) | No-go | W3C placeholder; not for production |
| [did:factom](../../machine-investigation/credentials/did-investigation/did-factom.md) | No-go | Requires Factom blockchain |
| [did:fairx](../../machine-investigation/credentials/did-investigation/did-fairx.md) | No-go | Spec unavailable; company shut down 2019 |
| [did:fox](../../machine-investigation/credentials/did-investigation/did-fox.md) | No-go | Requires Hyperledger Indy/Trustnet ledger access |
| [did:future](../../machine-investigation/credentials/did-investigation/did-future.md) | No-go | Netease proprietary blockchain |
| [did:gatc](../../machine-investigation/credentials/did-investigation/did-gatc.md) | No-go | Requires Gataca/Ethereum infrastructure |
| [did:gns](../../machine-investigation/credentials/did-investigation/did-gns.md) | No-go | Requires GNUnet DHT network |
| [did:grg](../../machine-investigation/credentials/did-investigation/did-grg.md) | No-go | Requires external HTTP resolver; Chinese focus |
| [did:grn](../../machine-investigation/credentials/did-investigation/did-grn.md) | No-go | Requires Grano blockchain |
| [did:gwm](../../machine-investigation/credentials/did-investigation/did-gwm.md) | No-go | Great Wall Motors consortium chain |
| [did:health](../../machine-investigation/credentials/did-investigation/did-health.md) | No-go | Requires Ethereum/ERC1056 |
| [did:hedera](../../machine-investigation/credentials/did-investigation/did-hedera.md) | No-go | Requires Hedera Hashgraph network |
| [did:hid](../../machine-investigation/credentials/did-investigation/did-hid.md) | No-go | Requires Hypersign/Cosmos blockchain |
| [did:holo](../../machine-investigation/credentials/did-investigation/did-holo.md) | No-go | Requires Holochain DHT infrastructure |
| [did:hpass](../../machine-investigation/credentials/did-investigation/did-hpass.md) | No-go | IBM; Hyperledger Fabric; healthcare focus |
| [did:hsk](../../machine-investigation/credentials/did-investigation/did-hsk.md) | No-go | Requires PlatON blockchain |
| [did:iamx](../../machine-investigation/credentials/did-investigation/did-iamx.md) | No-go | Requires Cardano blockchain |
| [did:ibmdc](../../machine-investigation/credentials/did-investigation/did-ibmdc.md) | No-go | IBM proprietary; limited public spec |
| [did:icon](../../machine-investigation/credentials/did-investigation/did-icon.md) | No-go | Requires ICON blockchain |
| [did:id](../../machine-investigation/credentials/did-investigation/did-id.md) | No-go | Mastercard centralized HTTP resolution |
| [did:iid](../../machine-investigation/credentials/did-investigation/did-iid.md) | No-go | Requires Inspur Chain (Chinese enterprise) |
| [did:io](../../machine-investigation/credentials/did-investigation/did-io.md) | No-go | Requires IoTeX blockchain |
| [did:iota](../../machine-investigation/credentials/did-investigation/did-iota.md) | No-go | Requires IOTA Tangle |
| [did:ipid](../../machine-investigation/credentials/did-investigation/did-ipid.md) | No-go | Requires IPFS/IPNS infrastructure |
| [did:is](../../machine-investigation/credentials/did-investigation/did-is.md) | No-go | Requires Blockcore blockchain |
| [did:iscc](../../machine-investigation/credentials/did-investigation/did-iscc.md) | No-go | Content identification; blockchain federation |
| [did:ishare](../../machine-investigation/credentials/did-investigation/did-ishare.md) | No-go | iSHARE Framework; PKI dependency |
| [did:itn](../../machine-investigation/credentials/did-investigation/did-itn.md) | No-go | Dual-ledger (Fabric + Arbitrum); complex |
| [did:iwt](../../machine-investigation/credentials/did-investigation/did-iwt.md) | No-go | Requires blockchain smart contracts |
| [did:jlinc](../../machine-investigation/credentials/did-investigation/did-jlinc.md) | No-go | Federated HTTP/ActivityPub resolvers |
| [did:jlinx](../../machine-investigation/credentials/did-investigation/did-jlinx.md) | No-go | HTTP/HTTPS endpoint dependency |
| [did:jnctn](../../machine-investigation/credentials/did-investigation/did-jnctn.md) | No-go | Spec not accessible (404) |
| [did:jolo](../../machine-investigation/credentials/did-investigation/did-jolo.md) | No-go | Ethereum + IPFS dual dependency |
| [did:jwks](../../machine-investigation/credentials/did-investigation/did-jwks.md) | No-go | Requires HTTP fetch to JWKS endpoints |
| [did:kaname](../../machine-investigation/credentials/did-investigation/did-kaname.md) | No-go | EVM + centralized API dependency |
| [did:kdid](../../machine-investigation/credentials/did-investigation/did-kdid.md) | No-go | FISCO BCOS; Chinese financial sector |
| [did:klay](../../machine-investigation/credentials/did-investigation/did-klay.md) | No-go | Requires Klaytn blockchain |
| [did:klayr](../../machine-investigation/credentials/did-investigation/did-klayr.md) | No-go | Requires Klayr sidechain |
| [did:knox](../../machine-investigation/credentials/did-investigation/did-knox.md) | No-go | Archived project; trusted operator model |
| [did:kr](../../machine-investigation/credentials/did-investigation/did-kr.md) | No-go | Korean government blockchain only |
| [did:kscirc](../../machine-investigation/credentials/did-investigation/did-kscirc.md) | No-go | Spec not accessible; KSChain dependent |
| [did:lac](../../machine-investigation/credentials/did-investigation/did-lac.md) | No-go | LACChain/Ethereum dependency |
| [did:ldid](../../machine-investigation/credentials/did-investigation/did-ldid.md) | No-go | Blockchain required for verification |
| [did:ling](../../machine-investigation/credentials/did-investigation/did-ling.md) | No-go | Spec not accessible (Notion) |
| [did:lit](../../machine-investigation/credentials/did-investigation/did-lit.md) | No-go | Requires LEDGIS blockchain |
| [did:m2m](../../machine-investigation/credentials/did-investigation/did-m2m.md) | No-go | Hyperledger Indy + ACCIO BaaS |
| [did:meme](../../machine-investigation/credentials/did-investigation/did-meme.md) | No-go | Experimental; IPFS; no key rotation |
| [did:meta](../../machine-investigation/credentials/did-investigation/did-meta.md) | No-go | Requires Metadium blockchain |
| [did:midnight](../../machine-investigation/credentials/did-investigation/did-midnight.md) | No-go | Requires Midnight blockchain |
| [did:moac](../../machine-investigation/credentials/did-investigation/did-moac.md) | No-go | Requires MOAC blockchain |
| [did:monid](../../machine-investigation/credentials/did-investigation/did-monid.md) | No-go | Ethereum + IPFS + Torus triple dependency |
| [did:morpheus](../../machine-investigation/credentials/did-investigation/did-morpheus.md) | No-go | Spec unavailable (410); Hydra blockchain |
| [did:mydata](../../machine-investigation/credentials/did-investigation/did-mydata.md) | No-go | Requires HTTP to ADA service endpoints |
| [did:myDiD](../../machine-investigation/credentials/did-investigation/did-mydid.md) | No-go | Requires EVM blockchain access |
| [did:nda](../../machine-investigation/credentials/did-investigation/did-nda.md) | No-go | Requires NDA Chain blockchain or resolver |
| [did:near](../../machine-investigation/credentials/did-investigation/did-near.md) | No-go | Requires NEAR blockchain; spec abandoned 2020 |
| [did:next](../../machine-investigation/credentials/did-investigation/did-next.md) | No-go | Requires Nextme platform infrastructure |
| [did:nft](../../machine-investigation/credentials/did-investigation/did-nft.md) | No-go | Requires blockchain and Ceramic Network access |
| [did:ns](../../machine-investigation/credentials/did-investigation/did-ns.md) | No-go | Requires DNS lookups to external infrastructure |
| [did:nuggets](../../machine-investigation/credentials/did-investigation/did-nuggets.md) | No-go | Requires HTTP to Nuggets proprietary API |
| [did:nuts](../../machine-investigation/credentials/did-investigation/did-nuts.md) | No-go | Requires participation in Nuts mesh network |
| [did:object](../../machine-investigation/credentials/did-investigation/did-object.md) | No-go | Requires Stratis blockchain access |
| [did:ockam](../../machine-investigation/credentials/did-investigation/did-ockam.md) | No-go | Requires Ockam Network; spec abandoned 2021 |
| [did:omn](../../machine-investigation/credentials/did-investigation/did-omn.md) | No-go | Requires OmniOne blockchain access |
| [did:onion](../../machine-investigation/credentials/did-investigation/did-onion.md) | No-go | Requires Tor network HTTP requests |
| [did:ont](../../machine-investigation/credentials/did-investigation/did-ont.md) | No-go | Requires Ontology blockchain access |
| [did:op](../../machine-investigation/credentials/did-investigation/did-op.md) | No-go | Requires Ethereum and Aquarius HTTP access |
| [did:operon](../../machine-investigation/credentials/did-investigation/did-operon.md) | No-go | Requires HTTP to Operon Cloud API |
| [did:orb](../../machine-investigation/credentials/did-investigation/did-orb.md) | No-go | Requires HTTP to federated Orb servers |
| [did:os](../../machine-investigation/credentials/did-investigation/did-os.md) | No-go | Requires blockchain RPC for ERC-1271 verification |
| [did:oyd](../../machine-investigation/credentials/did-investigation/did-oyd.md) | No-go | Requires HTTP to repository servers |
| [did:panacea](../../machine-investigation/credentials/did-investigation/did-panacea.md) | No-go | Requires Panacea blockchain access |
| [did:peaq](../../machine-investigation/credentials/did-investigation/did-peaq.md) | No-go | Requires peaq blockchain (Substrate) access |
| [did:pid](../../machine-investigation/credentials/did-investigation/did-pid.md) | No-go | Requires ProofID blockchain access |
| [did:pistis](../../machine-investigation/credentials/did-investigation/did-pistis.md) | No-go | Requires Ethereum blockchain; Ropsten deprecated |
| [did:plc](../../machine-investigation/credentials/did-investigation/did-plc.md) | No-go | Centralized PLC directory; Bluesky |
| [did:pml](../../machine-investigation/credentials/did-investigation/did-pml.md) | No-go | Centralized HTTP resolver |
| [did:polygon](../../machine-investigation/credentials/did-investigation/did-polygon.md) | No-go | Requires Polygon blockchain |
| [did:polygonid](../../machine-investigation/credentials/did-investigation/did-polygonid.md) | No-go | Blockchain for ZK proof verification |
| [did:psi](../../machine-investigation/credentials/did-investigation/did-psi.md) | No-go | Korean police; blockchain smart contracts |
| [did:psqr](../../machine-investigation/credentials/did-investigation/did-psqr.md) | No-go | DNS/HTTPS like did:web |
| [did:ptn](../../machine-investigation/credentials/did-investigation/did-ptn.md) | No-go | PalletOne blockchain + Sidetree |
| [did:qes](../../machine-investigation/credentials/did-investigation/did-qes.md) | No-go | Spec not available; eIDAS infrastructure |
| [did:qui](../../machine-investigation/credentials/did-investigation/did-qui.md) | No-go | Spec not accessible (404) |
| [did:ray](../../machine-investigation/credentials/did-investigation/did-ray.md) | No-go | RAY network blockchain + HTTP resolver |
| [did:real](../../machine-investigation/credentials/did-investigation/did-real.md) | No-go | Ethereum; no updates supported |
| [did:resume](../../machine-investigation/credentials/did-investigation/did-resume.md) | No-go | HTTP endpoints + ledger dependency |
| [did:rm](../../machine-investigation/credentials/did-investigation/did-rm.md) | No-go | Blockchain + HTTP endpoints |
| [did:safe](../../machine-investigation/credentials/did-investigation/did-safe.md) | No-go | EVM + Ceramic + The Graph |
| [did:san](../../machine-investigation/credentials/did-investigation/did-san.md) | No-go | SAN blockchain + HTTP resolver |
| [did:schema](../../machine-investigation/credentials/did-investigation/did-schema.md) | No-go | IPFS dependency for schema storage |
| [did:scid](../../machine-investigation/credentials/did-investigation/did-scid.md) | No-go | Requires StraitsChain blockchain |
| [did:selfkey](../../machine-investigation/credentials/did-investigation/did-selfkey.md) | No-go | Ethereum smart contracts |
| [did:sideos](../../machine-investigation/credentials/did-investigation/did-sideos.md) | No-go | Ledger infrastructure required |
| [did:signor](../../machine-investigation/credentials/did-investigation/did-signor.md) | No-go | Ethereum smart contracts |
| [did:sirius](../../machine-investigation/credentials/did-investigation/did-sirius.md) | No-go | ProximaX Sirius Chain |
| [did:snail](../../machine-investigation/credentials/did-investigation/did-snail.md) | No-go | Experimental; no documentation |
| [did:snplab](../../machine-investigation/credentials/did-investigation/did-snplab.md) | No-go | Hyperledger Fabric + REST API |
| [did:sol](../../machine-investigation/credentials/did-investigation/did-sol.md) | No-go | Requires Solana blockchain |
| [did:sov](../../machine-investigation/credentials/did-investigation/did-sov.md) | No-go | Sovrin/Indy ledger dependency |
| [did:ssb](../../machine-investigation/credentials/did-investigation/did-ssb.md) | No-go | SSB peer-to-peer network; draft |
| [did:ssw](../../machine-investigation/credentials/did-investigation/did-ssw.md) | No-go | SK telecom consortium (Korea) |
| [did:stack](../../machine-investigation/credentials/did-investigation/did-stack.md) | No-go | Bitcoin + Atlas + Blockstack nodes |
| [did:tangle](../../machine-investigation/credentials/did-investigation/did-tangle.md) | No-go | IOTA Tangle + MAM channels |
| [did:tdid](../../machine-investigation/credentials/did-investigation/did-tdid.md) | No-go | Tencent Cloud; vendor-locked |
| [did:theseries](../../machine-investigation/credentials/did-investigation/did-theseries.md) | No-go | Ethereum/EVM + ERC725 |
| [did:ti](../../machine-investigation/credentials/did-investigation/did-ti.md) | No-go | TiChain + Tianhe Cloud API |
| [did:tls](../../machine-investigation/credentials/did-investigation/did-tls.md) | No-go | No spec available; likely PKI |
| [did:tr](../../machine-investigation/credentials/did-investigation/did-tr.md) | No-go | Hyperledger Indy (Turkey) |
| [did:trust](../../machine-investigation/credentials/did-investigation/did-trust.md) | No-go | Trustchain blockchain network |
| [did:trustbloc](../../machine-investigation/credentials/did-investigation/did-trustbloc.md) | No-go | Sidetree + consortium infrastructure |
| [did:trx](../../machine-investigation/credentials/did-investigation/did-trx.md) | No-go | Requires TRON blockchain |
| [did:ttm](../../machine-investigation/credentials/did-investigation/did-ttm.md) | No-go | Requires TMChain blockchain |
| [did:twit](../../machine-investigation/credentials/did-investigation/did-twit.md) | No-go | Twitter dependency; experimental |
| [did:tyron](../../machine-investigation/credentials/did-investigation/did-tyron.md) | No-go | Requires Zilliqa blockchain |
| [did:tys](../../machine-investigation/credentials/did-investigation/did-tys.md) | No-go | Hyperledger Fabric consortium |
| [did:unik](../../machine-investigation/credentials/did-investigation/did-unik.md) | No-go | uns.network blockchain |
| [did:unisot](../../machine-investigation/credentials/did-investigation/did-unisot.md) | No-go | Bitcoin SV blockchain |
| [did:unitrust](../../machine-investigation/credentials/did-investigation/did-unitrust.md) | No-go | Shanghai Identity Chain + Chinese CA |
| [did:uns](../../machine-investigation/credentials/did-investigation/did-uns.md) | No-go | uns.network (ARK.io) blockchain |
| [did:uport](../../machine-investigation/credentials/did-investigation/did-uport.md) | No-go | Deprecated; Ethereum dependency |
| [did:v1](../../machine-investigation/credentials/did-investigation/did-v1.md) | No-go | Veres One ledger required |
| [did:vaa](../../machine-investigation/credentials/did-investigation/did-vaa.md) | No-go | BIF blockchain (China) |
| [did:valyu](../../machine-investigation/credentials/did-investigation/did-valyu.md) | No-go | Spec unavailable (404); abandoned |
| [did:vaultie](../../machine-investigation/credentials/did-investigation/did-vaultie.md) | No-go | Ethereum + IPFS dual dependency |
| [did:vertu](../../machine-investigation/credentials/did-investigation/did-vertu.md) | No-go | VERTU hardware/ecosystem only |
| [did:vid](../../machine-investigation/credentials/did-investigation/did-vid.md) | No-go | VP blockchain; incomplete spec |
| [did:vivid](../../machine-investigation/credentials/did-investigation/did-vivid.md) | No-go | Neo/Zilliqa blockchain required |
| [did:vtid](../../machine-investigation/credentials/did-investigation/did-vtid.md) | No-go | JianKong network (VERTU) |
| [did:vvo](../../machine-investigation/credentials/did-investigation/did-vvo.md) | No-go | Vivvo private ledger |
| [did:wba](../../machine-investigation/credentials/did-investigation/did-wba.md) | No-go | DNS/HTTPS like did:web |
| [did:web](../../machine-investigation/credentials/did-investigation/did-web.md) | No-go | Requires HTTP/DNS; cannot verify on-ledger |
| [did:web7](../../machine-investigation/credentials/did-investigation/did-web7.md) | No-go | Requires Web 7.0 network nodes; only 32K IDs |
| [did:webplus](../../machine-investigation/credentials/did-investigation/did-webplus.md) | No-go | HTTPS/DNS; cryptographic chain adds verifiability |
| [did:wlk](../../machine-investigation/credentials/did-investigation/did-wlk.md) | No-go | Weelink blockchain + gRPC |
| [did:work](../../machine-investigation/credentials/did-investigation/did-work.md) | No-go | Workday proprietary; spec unavailable (404) |
| [did:yourd](../../machine-investigation/credentials/did-investigation/did-yourd.md) | No-go | Multi-chain + local storage hybrid |
| [did:zk](../../machine-investigation/credentials/did-investigation/did-zk.md) | No-go | Arweave; no update/delete support |
| [did:zkme](../../machine-investigation/credentials/did-investigation/did-zkme.md) | No-go | Zetachain; testnet only |
