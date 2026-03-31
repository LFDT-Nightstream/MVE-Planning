# DApp-Wallet Connection Standards Across Blockchain Ecosystems

A comprehensive survey of how different blockchain ecosystems standardize the interface between decentralized applications (dApps) and wallet software. Each ecosystem has developed its own conventions, formal specifications, or both — reflecting different design philosophies around chain-specific richness, multi-wallet discovery, and transport flexibility.

---

## Cardano: CIP-30 (Cardano dApp-Wallet Web Bridge)

**Specification:** [CIP-30](https://cips.cardano.org/cip/CIP-0030)
**Status:** Active
**Injection Model:** `window.cardano.{walletName}`

CIP-30 defines a JavaScript API injected into the browser by wallet extensions, serving as the web bridge between Cardano dApps and wallet providers. The API is permission-gated: dApps call `cardano.{walletName}.enable()` and the wallet returns an API object scoped to the user's approved permissions.

### Core API Surface

The enabled API exposes 12 Cardano-specific methods:

- `getExtensions()` — retrieve enabled CIP-30 extensions
- `getNetworkId()` — return the network ID (mainnet/testnet)
- `getUtxos()` — query available UTxOs
- `getBalance()` — retrieve wallet balance (multi-asset aware)
- `getUsedAddresses()` / `getUnusedAddresses()` — address management
- `getChangeAddress()` — retrieve an address for transaction change
- `getRewardAddresses()` — retrieve staking/reward addresses
- `getCollateral()` — Plutus script collateral *(deprecated in current spec)*
- `signTx()` — request transaction signing
- `submitTx()` — submit a signed transaction
- `signData()` — CIP-8 message signing

### Extension CIPs

CIP-30 has been extended by several follow-up proposals:

- **CIP-95** — Conway-era governance capabilities (DRep registration, voting)
- **CIP-103** — Bulk transaction signing
- **CIP-104** — Account public key exposure
- **CIP-106** — Multisig wallet support
- **CIP-141** — Plutus wallet support
- **CIP-142** — Network determination

### Design Characteristics

CIP-30 is the most **chain-model-specific** standard in this survey. It directly exposes Cardano's eUTxO model, multi-asset ledger, and Plutus scripting infrastructure in the API. This gives dApps rich, typed access to chain state but couples the specification tightly to Cardano's ledger semantics.

The `window.cardano` object uses **namespaced keys** per wallet (e.g., `window.cardano.nami`, `window.cardano.eternl`), which partially mitigates the multi-wallet collision problem that plagued early Ethereum.

---

## Ethereum: EIP-1193 + EIP-6963

### EIP-1193: Ethereum Provider JavaScript API

**Specification:** [EIP-1193](https://eips.ethereum.org/EIPS/eip-1193)
**Status:** Final (proposed June 2018)
**Authors:** Fabian Vogelsteller, Ryan Ghods, Victor Maia, Marc Garreau, Erik Marks
**Injection Model:** `window.ethereum`

EIP-1193 is the foundational standard for Ethereum dApp-wallet communication. It formalizes an Ethereum Provider API designed to be minimal, event-driven, and agnostic of transport and RPC protocols. Before EIP-1193, wallets injected various objects with different naming conventions and conflicting interfaces, creating a fragmented landscape of proprietary quirks.

#### Core Interface

The API surface is deliberately minimal:

- `request({ method, params })` — single RPC passthrough method (promise-based)
- `on('chainChanged', handler)` — chain switch events
- `on('accountsChanged', handler)` — account change events
- `on('connect', handler)` / `on('disconnect', handler)` — connectivity events
- `on('message', handler)` — arbitrary notifications (e.g., `eth_subscribe` results)

All chain-specific logic is delegated to RPC method names (e.g., `eth_sendTransaction`, `eth_accounts`, `eth_chainId`). This makes EIP-1193 extensible — new capabilities are added by defining new RPC methods, not by modifying the provider interface.

#### The `window.ethereum` Problem

EIP-1193 historically relied on wallets injecting a provider object as `window.ethereum`. When multiple wallets are installed, they compete for control of this single global — Coinbase Wallet, for example, often overrides MetaMask's implementation by default. Wallets attempted workarounds (e.g., `window.ethereum.providers` arrays, wallet-specific globals like `window.tokenPocket`), but none were standardized.

### EIP-6963: Multi-Injected Provider Discovery

**Specification:** [EIP-6963](https://eips.ethereum.org/EIPS/eip-6963)
**Status:** Final
**Purpose:** Solves the multi-wallet `window.ethereum` collision

EIP-6963 introduces a browser event-based discovery mechanism. Wallets announce themselves by dispatching `eip6963:announceProvider` events carrying an `EIP6963ProviderDetail` object composed of two parts:

- `info` — an `EIP6963ProviderInfo` object containing:
  - `info.rdns` — reverse domain name identifier (e.g., `io.metamask`)
  - `info.uuid` — globally unique session ID
  - `info.name` — human-readable wallet name
  - `info.icon` — wallet icon URL (data URI)
- `provider` — the EIP-1193 provider instance

DApps listen for these events and present all discovered wallets to the user. While EIP-6963 changes how wallets are discovered, actual communication still uses the EIP-1193 `request()` interface underneath.

### Related Ethereum Standards

- **EIP-4361 (Sign-In with Ethereum / SIWE):** Authentication standard for message signing, analogous to web2 OAuth flows. Defines a structured plaintext message format for wallet-based authentication.
- **EIP-712:** Typed structured data signing for human-readable transaction approval. Allows wallets to display structured, domain-separated data for user review before signing.
- **EIP-155:** Chain ID specification used for replay protection and chain identification.

### Architectural Notes

EIP-1193 is more **minimal and generic** than CIP-30 — it's essentially an RPC passthrough that makes no assumptions about the underlying chain model. CIP-30 exposes Cardano-specific concepts (UTxOs, collateral, multi-asset balances) directly in the API. EIP-1193 achieves equivalent functionality through standardized RPC method names, but the provider interface itself is chain-agnostic.

---

## Solana: Wallet Standard + Mobile Wallet Adapter

Solana takes a **layered approach** with multiple complementary standards addressing different use cases (browser, mobile, authentication).

### 1. Solana Wallet Standard

**Repository:** [anza-xyz/wallet-standard](https://github.com/anza-xyz/wallet-standard) (formerly solana-labs/wallet-standard)
**Purpose:** Cross-wallet interface specification for browser-based dApps

Instead of fighting over `window.solana` (Solana's equivalent of the `window.ethereum` collision), wallets register themselves in a standard registry. The interface uses **feature-namespaced capabilities:**

- `standard:connect` — establish connection
- `standard:disconnect` — terminate connection
- `standard:events` — event subscription
- `solana:signMessage` — message signing
- `solana:signTransaction` — transaction signing
- `solana:signAndSendTransaction` — sign and submit
- `solana:signIn` — Sign In With Solana (SIWS) authentication

Wallets implement the standard by wrapping their existing API and calling an `initialize()` function that registers the wallet with the Wallet Standard registry. DApps discover available wallets through registry queries rather than global object inspection.

This is architecturally more similar to **EIP-6963's event-based discovery** than to EIP-1193's single-provider model — both Solana and modern Ethereum have converged on registry/announcement patterns to solve the multi-wallet problem.

### 2. Solana Wallet Adapter

**Repository:** [solana-labs/wallet-adapter](https://github.com/solana-labs/wallet-adapter)
**Purpose:** Practical UI library wrapping the Wallet Standard

The Wallet Adapter is the de facto integration layer most Solana dApp developers use. It provides:

- Pre-built React, Vue, Angular, and Svelte components
- Wallet selection modal UI
- Context providers for wallet state management
- Auto-connect / reconnection logic

It wraps the Wallet Standard and presents a unified interface regardless of which underlying wallet the user selects.

### 3. Mobile Wallet Adapter (MWA) 2.0

**Specification:** [MWA 2.0 Spec](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html)
**Purpose:** Native mobile dApp ↔ wallet communication

MWA is unique among the ecosystems surveyed. It defines a protocol for connecting mobile dApps to mobile wallet apps using:

- **URI Scheme:** `solana-wallet:/v1/associate/local?association=<token>&port=<port>&v=<version>`
- **Session Establishment:** Ephemeral EC keypairs on the P-256 curve, with the public key base64url-encoded as the association token
- **Transport:** Local WebSocket connections for same-device communication; reflector WebSocket servers for remote/cross-device connections
- **Versioning:** Protocol version negotiated via URI path segment and query parameters

MWA exposes mandatory features (`solana:signTransactions`, `solana:signAndSendTransactions`) and optional features (`solana:signInWithSolana`, `solana:signMessages`). The protocol is ephemeral — associations persist only until the transport disconnects.

This has no real equivalent in Cardano or Ethereum's core standards. It's specifically engineered for the mobile-first use case where the dApp and wallet run as separate native apps.

### 4. Sign In With Solana (SIWS)

**Repository:** [phantom/sign-in-with-solana](https://github.com/phantom/sign-in-with-solana)
**Purpose:** Combined authentication + authorization

Unlike EIP-4361 (SIWE) or legacy Solana message signing, SIWS does not require dApps to construct the message themselves. Instead, dApps construct a `signInInput` object with 12 standard message parameters (all optional):

- `domain` — requesting domain
- `address` — Solana address performing sign-in
- `statement` — human-readable statement
- `uri` — requesting URI
- Additional EIP-4361-compatible fields: `version`, `chainId`, `nonce`, `issuedAt`, `expirationTime`, `notBefore`, `requestId`, `resources`

The wallet constructs an ABNF-formatted message from these parameters, signs it, and returns the message, signature, and public address. This combines the authorization (`connect`) and authentication (`signMessage`) steps into a single interaction.

### Architectural Notes

Solana's approach is more **modular** than CIP-30 — separate specs handle discovery (Wallet Standard), desktop UI (Wallet Adapter), mobile transport (MWA), and authentication (SIWS). CIP-30 combines discovery, API, and transaction signing in a single specification. Solana also doesn't need UTxO-specific API methods since it uses an account model.

---

## Polkadot/Substrate: `injectedWeb3` Convention

**De Facto Standard:** Polkadot{.js} extension injection pattern
**Injection Model:** `window.injectedWeb3.{walletName}`
**Formal Specification:** No single canonical standard document (convention-driven)

Polkadot doesn't have a single formal standard document equivalent to CIP-30 or EIP-1193. Instead, it uses a de facto convention established by the Polkadot{.js} extension.

### Injection Pattern

Wallet extensions (Polkadot{.js}, SubWallet, Talisman, etc.) modify `window.injectedWeb3` by adding their interaction object under a specific name. For example:

```javascript
// Check for SubWallet extension
window.injectedWeb3 && window.injectedWeb3['subwallet-js']
```

After calling `enable()` on a wallet's interaction object, the extension returns:

- **`accounts`** — `get()` and `subscribe()` methods for account data
- **`signer`** — `signPayload()` and `signRaw()` methods for transaction/message signing
- **`metadata`** *(optional)* — `get()` and `provide()` methods for chain metadata management
- **`provider`** *(optional)* — RPC provider injection for direct node communication

### Integration Libraries

Because the convention is informal, several libraries provide unified abstractions:

- **DOTConnect** — a framework-agnostic web component library providing UI widgets for managing Polkadot wallet connections, supporting all major ecosystem wallets (Polkadot{.js}, SubWallet, Talisman, Nova Wallet, PolkaGate, Enkrypt, Fearless, Mimir). Often used alongside **ReactiveDOT** (`@reactive-dot/react`), which provides the React hooks layer.
- **@polkadot/extension-dapp** — the core library from Polkadot{.js} for interacting with browser extensions

### WalletConnect Integration

Polkadot also supports WalletConnect v2 using CAIP-13 chain identification (e.g., `polkadot:91b171bb158e2d3848fa23a9f1c25182` for Polkadot mainnet). The WalletConnect integration uses `polkadot_signTransaction` and `polkadot_signMessage` as the RPC methods within the WalletConnect session namespace.

### Architectural Notes

The `injectedWeb3` approach is structurally **very similar to CIP-30's `window.cardano` pattern** — both use namespace-keyed injection with an `enable()` gate. Polkadot's signer interface is thinner, focused on signing payloads and raw bytes rather than exposing chain-specific state like UTxOs. The lack of a formal specification means wallet behavior is more convention-driven, with interoperability maintained through shared library usage rather than spec compliance.

---

## Tezos: TZIP-10 + Beacon SDK

**Specification:** [TZIP-10](https://gitlab.com/tezos/tzip/-/blob/master/proposals/tzip-10/tzip-10.md) (Wallet Interaction Standard)
**Implementation:** [Beacon SDK](https://github.com/airgap-it/beacon-sdk) (also forked as [Octez Connect](https://github.com/trilitech/octez.connect) by Trilitech)
**Purpose:** Transport-agnostic dApp-wallet communication

### Architecture

Tezos dApps connect via **Beacon**, a JavaScript/TypeScript SDK that implements the TZIP-10 proposal. TZIP-10 describes an interaction standard between wallets and dApps, specifying a peer-to-peer communication layer that enables cross-platform connections.

Key capabilities:

- A user can scan a QR code on a mobile app and use the dApp on desktop
- Beacon supports TZIP-10 wallets, WalletConnect 2.0 wallets, and browser extension wallets through a single interface
- **Taquito** provides an additional wrapper layer combining wallet interaction with on-chain operations (smart contract calls, token transfers, etc.)

### Core Interaction Flow

```javascript
import { BeaconWallet } from "@taquito/beacon-wallet";

const wallet = new BeaconWallet({
  name: "My dApp",
  preferredNetwork: network
});

// Check for existing connection
const activeAccount = await wallet.client.getActiveAccount();

// Disconnect
wallet.client.clearActiveAccount();
```

### Design Characteristics

TZIP-10/Beacon is more **transport-aware** than CIP-30. While CIP-30 assumes browser extension injection as the primary (essentially only) transport, Beacon abstracts over multiple transport mechanisms:

- Peer-to-peer messaging (via Matrix protocol or similar)
- WalletConnect 2.0 relay
- Browser extension injection
- QR code / deep link initiation

This makes Tezos's approach more similar to Solana's MWA or WalletConnect v2 in its transport flexibility, while maintaining a simpler API surface than either.

---

## Cross-Chain: WalletConnect v2 + CAIP Standards

**Specification:** [WalletConnect v2 Specs](https://specs.walletconnect.com/2.0/)
**CAIP Standards:** [Chain Agnostic Standards Alliance](https://github.com/ChainAgnostic/CAIPs)
**Rebranded As:** Reown (September 2024; protocol and network names remain WalletConnect)
**Purpose:** Universal cross-ecosystem dApp-wallet communication

WalletConnect v2 is the closest thing to a universal cross-ecosystem dApp-wallet connection standard. It operates as an encrypted communication protocol that establishes sessions between dApps and wallets via relay servers.

### Evolution from v1 to v2

WalletConnect v1.0 assumed both parties were communicating about EVM-compatible chains using the EIP-155 chainId standard, making chains like Cosmos and Polkadot incompatible. This led to the Chain Agnostic Improvement Proposals (CAIPs) initiative in 2019, developed collaboratively with projects across Ethereum, Cosmos, Polkadot, Solana, NEAR, Filecoin, and others.

### Key CAIP Standards

- **CAIP-2 (Blockchain ID):** Universal chain identification format
  - Ethereum mainnet: `eip155:1`
  - Bitcoin mainnet: `bip122:000000000019d6689c085ae165831e93`
  - Cosmos Hub: `cosmos:cosmoshub-4`
  - Kusama: `polkadot:b0a8d493285c2df73290dfb7e61f870f`

- **CAIP-10 (Account ID):** Chain-prefixed account addresses
  - `eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb`
  - `cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0`

- **CAIP-25 (Authorization):** Chain-agnostic provider initialization and handshake protocol

- **CAIP-217 (Authorization Scope):** Defines session authorization scopes. Conceptually analogous to IETF's Rich Authorization Requests (RAR) for OAuth 2.0 (RFC 9396), though CAIP-217 does not explicitly cite RAR as a design influence.

### Session Namespaces

WalletConnect v2's session proposal mechanism uses `requiredNamespaces` and `optionalNamespaces` to negotiate which chains, RPC methods, and events a session supports:

```javascript
const params = {
  requiredNamespaces: {
    eip155: {
      chains: ["eip155:1", "eip155:137"],
      methods: ["eth_sendTransaction", "personal_sign"],
      events: ["accountsChanged", "chainChanged"]
    },
    cosmos: {
      chains: ["cosmos:cosmoshub-4"],
      methods: ["cosmos_signDirect"],
      events: []
    }
  }
};
```

This approach is chain-, event-, method-, and wallet-agnostic, enabling adoption by EOA wallets, smart contract wallets, or any other wallet type. It eliminates the need to establish separate sessions for each chain when interacting with cross-chain dApps.

### Connection Flow

1. DApp generates a session proposal with namespace requirements
2. Proposal is encoded as a QR code or deep link
3. Wallet scans/processes the proposal and displays connection details
4. User approves or rejects the connection
5. Both parties establish a shared encryption key via X25519 Diffie-Hellman key exchange
6. Encrypted bidirectional communication flows through relay servers
7. Relay servers function as message routers without decryption capabilities

### Ecosystem Support

WalletConnect v2 supports 150+ blockchain networks as of 2026. The protocol uses JSON-RPC 2.0 for message serialization. Each supported ecosystem defines its own namespace-specific RPC methods (e.g., `polkadot_signTransaction`, `cosmos_signDirect`, `solana_signTransaction`).

---

## Comparative Analysis

### Axis 1: Chain-Specific Richness vs. Chain-Agnostic Generality

| Standard | Specificity | Notes |
|----------|------------|-------|
| CIP-30 | Highest | Directly exposes eUTxO model, multi-asset balances, collateral, Plutus data |
| Polkadot `injectedWeb3` | Medium | Exposes substrate-specific signing (payload vs. raw), metadata management |
| Solana Wallet Standard | Medium | Feature-namespaced capabilities specific to Solana's account model |
| EIP-1193 | Low | Generic RPC passthrough; chain semantics live in method names |
| TZIP-10/Beacon | Low-Medium | Tezos-specific operations abstracted behind Taquito |
| WalletConnect v2/CAIP | Lowest | Fully chain-agnostic; delegates all semantics to namespace-specific RPC methods |

### Axis 2: Wallet Discovery Mechanism

| Standard | Mechanism | Multi-Wallet Support |
|----------|-----------|---------------------|
| CIP-30 | Namespaced `window.cardano.{name}` | Native (namespace isolation) |
| EIP-1193 (original) | Single `window.ethereum` | Poor (wallets fight for global) |
| EIP-6963 | Browser event announcement | Native (event-based registry) |
| Solana Wallet Standard | Registry-based | Native (explicit registration) |
| Polkadot `injectedWeb3` | Namespaced `window.injectedWeb3.{name}` | Native (namespace isolation) |
| TZIP-10/Beacon | SDK-mediated discovery | Abstracted by Beacon SDK |
| WalletConnect v2 | QR code / deep link / relay | N/A (point-to-point sessions) |

### Axis 3: Transport Flexibility

| Standard | Browser Extension | Mobile Native | Cross-Device | P2P Relay |
|----------|:-:|:-:|:-:|:-:|
| CIP-30 | ✅ | ❌ | ❌ | ❌ |
| EIP-1193 | ✅ | ❌ | ❌ | ❌ |
| Solana Wallet Standard | ✅ | ❌ | ❌ | ❌ |
| Solana MWA 2.0 | ❌ | ✅ | ✅ (via reflector) | ❌ |
| Polkadot `injectedWeb3` | ✅ | ❌ | ❌ | ❌ |
| TZIP-10/Beacon | ✅ | ✅ | ✅ | ✅ (Matrix) |
| WalletConnect v2 | ❌ | ✅ | ✅ | ✅ (Waku/relay) |

### Axis 4: Specification Formality

| Standard | Formal Spec | Governance Process |
|----------|------------|-------------------|
| CIP-30 | Yes (CIP process) | Cardano Improvement Proposal |
| EIP-1193 | Yes (EIP process) | Ethereum Improvement Proposal |
| EIP-6963 | Yes (EIP process) | Ethereum Improvement Proposal |
| Solana Wallet Standard | Partial (README + code) | GitHub repository |
| Solana MWA 2.0 | Yes (detailed spec document) | Solana Mobile |
| Polkadot `injectedWeb3` | No (de facto convention) | Convention via Polkadot{.js} |
| TZIP-10 | Yes (TZIP process) | Tezos Improvement Proposal |
| WalletConnect v2 | Yes (detailed specs) | WalletConnect / Reown |
| CAIP standards | Yes (CAIP process) | Chain Agnostic Standards Alliance |

---

## Key Design Tensions

### 1. API Richness vs. Portability

CIP-30's approach of exposing chain-specific primitives (UTxOs, collateral, multi-asset balances) gives dApp developers rich, typed access to chain state — but it means the API is inherently non-portable. EIP-1193's minimal RPC-passthrough design sacrifices ergonomics for universality. The optimal point on this spectrum depends on whether cross-chain tooling (like WalletConnect) or ecosystem-specific developer experience is prioritized.

### 2. Extension-Only vs. Transport-Agnostic

CIP-30, EIP-1193, and Polkadot's `injectedWeb3` all assume browser extension injection. This creates a hard dependency on desktop browsers with extension support. Solana's MWA, Tezos's Beacon, and WalletConnect v2 demonstrate that abstracting over transport mechanisms (WebSocket, QR, deep links, P2P relay) dramatically expands the contexts in which dApps can operate — particularly mobile-native and cross-device scenarios.

### 3. Single-Spec vs. Layered Standards

Cardano bundles discovery + API + signing in CIP-30 (with extensions via CIP-95, CIP-103, etc.). Solana separates these into distinct standards (Wallet Standard, Wallet Adapter, MWA, SIWS). Ethereum evolved from a monolithic EIP-1193 toward a layered stack (EIP-1193 + EIP-6963 + EIP-4361 + EIP-712). The layered approach is more complex but allows independent evolution of each concern.

### 4. Privacy Implications

None of the surveyed standards were designed with privacy-preserving blockchains in mind. For ecosystems like Midnight Network, a dApp connector standard would need to address:

- Shielded vs. transparent state exposure
- Selective disclosure of balances and UTxOs
- Zero-knowledge proof generation coordination between dApp and wallet
- Credential and identity management within ZK circuits

This represents a largely unexplored design space in dApp-wallet interface standardization.

---

## References

- [CIP-30: Cardano dApp-Wallet Web Bridge](https://cips.cardano.org/cip/CIP-0030)
- [EIP-1193: Ethereum Provider JavaScript API](https://eips.ethereum.org/EIPS/eip-1193)
- [EIP-6963: Multi Injected Provider Discovery](https://eips.ethereum.org/EIPS/eip-6963)
- [Solana Wallet Standard (anza-xyz)](https://github.com/anza-xyz/wallet-standard)
- [Solana Mobile Wallet Adapter 2.0 Specification](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html)
- [Sign In With Solana (Phantom)](https://github.com/phantom/sign-in-with-solana)
- [Polkadot{.js} Extension](https://polkadot.js.org/docs/extension/)
- [SubWallet Integration Instructions](https://docs.subwallet.app/main/integration/integration-instructions)
- [TZIP-10: Wallet Interaction Standard](https://gitlab.com/tezos/tzip/-/blob/master/proposals/tzip-10/tzip-10.md)
- [Beacon SDK (AirGap)](https://github.com/airgap-it/beacon-sdk)
- [WalletConnect v2 Specifications](https://specs.walletconnect.com/2.0/)
- [Chain Agnostic Improvement Proposals (CAIPs)](https://github.com/ChainAgnostic/CAIPs)
- [CAIP-25: JSON-RPC Provider Authorization](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-25.md)

---

*Document compiled March 2026. Standards are actively evolving — consult primary sources for the latest revisions.*
