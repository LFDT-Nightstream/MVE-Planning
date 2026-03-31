# Midnight Wallet SDK Reference

<!-- Assembled by tools/assemble-wallet-docs.mjs -->
<!-- Source: upstream/midnight-wallet @ a1da64624b6a49aeed66d4f6466dee51fbab98ef -->
<!-- Generated: 2026-03-21T21:35:12.098Z -->

## 1. Introduction

The Midnight Wallet SDK is a TypeScript library for managing all three
token types on the Midnight blockchain: **shielded tokens** (zero-knowledge
privacy-preserving), **unshielded tokens** (Night, the native token), and
**dust** (fee tokens with time-dependent generation). The SDK provides
HD key derivation, address encoding, transaction balancing and signing,
chain synchronization, and ZK proof generation — all through a unified
facade.

### 1.1 Architecture Overview

The SDK follows a layered architecture with 13 packages:

```
Layer 1: Foundation
  abstractions    — Branded types (WalletSeed, WalletState, ProtocolVersion, ...)
  utilities       — Domain-agnostic operations (SafeBigInt, BlobOps, ...)
  hd              — HD wallet key derivation (BIP32/BIP39, CIP-1852)
  address-format  — Bech32m address encoding (mn_ prefix)

Layer 2: Infrastructure Clients
  node-client     — Polkadot RPC client for the Midnight node
  indexer-client  — GraphQL client for chain indexer
  prover-client   — HTTP client for ZK proof server

Layer 3: Capabilities
  capabilities    — Shared services and pure operations
    /balancer     — Coin selection and balance recipes
    /submission   — Transaction submission lifecycle
    /pendingTransactions — Pending transaction tracking
    /proving      — ZK proof generation services

Layer 4: Wallet Implementations
  shielded-wallet       — Shielded (private) token wallet
  unshielded-wallet     — Unshielded (Night) token wallet
  dust-wallet           — Dust (fee) token wallet

Layer 5: Facade
  facade          — Unified API combining all three wallets
```

### 1.2 Token Model

Midnight implements three distinct token types:

| Token | Wallet | Privacy | Purpose |
|-------|--------|---------|---------|
| Custom shielded | ShieldedWallet | ZK-private | Arbitrary tokens with zero-knowledge proofs |
| Night (unshielded) | UnshieldedWallet | Public | Native token for transfers and staking |
| Dust | DustWallet | Public | Fee token with time-dependent generation |

### 1.3 Variant/Runtime Pattern

Each wallet uses a **variant-based architecture** for protocol version
compatibility. A variant is a specific implementation for a range of
protocol versions (enabling hard-fork migration). The `WalletBuilder`
registers variants, and the `Runtime` dispatches to the correct one
based on the current protocol version.

### 1.4 Effect Library Integration

The SDK uses the [Effect](https://effect.website/) library extensively.
Most services have two flavors:

- **Effect-based** (`*Effect`): Returns `Effect.Effect<T, E>` — composable,
  lazy, with typed errors. Used internally and for advanced integrations.
- **Promise-based**: Returns `Promise<T>` — standard async/await API for
  most consumers.

All errors extend `Data.TaggedError`, providing a `_tag` discriminant
for pattern matching.

### 1.5 Evidence Summary

| Tier | Passing | Failing | Errors | Total |
|------|---------|---------|--------|-------|
| tier1 | 95 | 0 | 0 | 95 |
| tier3 | 0 | 0 | 1 | 1 |
| **Total** | **95** | **1** | | **96** |

---

## 2. API Surface

### @midnight-ntwrk/wallet-sdk-abstractions (2.0.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `WalletSeed` | namespace | — |
| `WalletState` | namespace | — |
| `SerializedTransaction` | namespace | — |
| `ProtocolState` | namespace | — |
| `ProtocolVersion` | namespace | — |
| `NetworkId` | namespace | — |
| `SyncProgress` | namespace | — |

### @midnight-ntwrk/wallet-sdk-address-format (3.1.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `mainnet` | const | — |
| `NetworkId` | type | — |
| `FormatContext` | type | — |
| `Field` | type | — |
| `BLSScalar` | const | — |
| `ScaleBigInt` | const | — |
| `Bech32mSymbol` | const | — |
| `HasCodec` | type | — |
| `CodecTarget` | type | — |
| `MidnightBech32m` | class | — |
| `Bech32mCodec` | class | — |
| `ShieldedAddress` | class | — |
| `ShieldedEncryptionSecretKey` | class | — |
| `ShieldedCoinPublicKey` | class | — |
| `ShieldedEncryptionPublicKey` | class | — |
| `UnshieldedAddress` | class | — |
| `DustAddress` | class | — |

### @midnight-ntwrk/wallet-sdk-capabilities (3.2.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `InsufficientFundsError` | class | — |
| `BalanceRecipe` | interface | — |
| `CoinSelection` | type | — |
| `BalanceRecipeProps` | type | — |
| `getBalanceRecipe` | const | — |
| `createCounterOffer` | const | — |
| `chooseCoin` | const | — |
| `TransactionCostModel` | interface | — |
| `CounterOffer` | class | — |
| `TokenType` | type | — |
| `TokenValue` | type | — |
| `CoinRecipe` | interface | — |
| `Imbalance` | type | — |
| `Imbalances` | type | — |
| `PendingTransactionsService` | type | — |
| `IndexerClientConnection` | type | — |
| `DefaultPendingTransactionsServiceConfiguration` | type | — |
| `InitParams` | type | — |
| `PendingTransactionsServiceImpl` | class | — |
| `PendingTransactionsServiceEffect` | type | — |
| `PendingTransactionsServiceEffectImpl` | class | — |
| `PendingTransactions` | namespace | — |
| `ProvingError` | class | — |
| `ProvingServiceEffect` | interface | — |
| `ProvingService` | interface | — |
| `UnboundTransaction` | type | — |
| `fromProvingProviderEffect` | const | — |
| `fromProvingProvider` | const | — |
| `ServerProvingConfiguration` | type | — |
| `WasmProvingConfiguration` | type | — |
| `DefaultProvingConfiguration` | type | — |
| `makeServerProvingServiceEffect` | const | — |
| `makeWasmProvingServiceEffect` | const | — |
| `makeSimulatorProvingServiceEffect` | const | — |
| `makeDefaultProvingServiceEffect` | const | — |
| `makeDefaultProvingService` | const | — |
| `makeServerProvingService` | const | — |
| `makeWasmProvingService` | const | — |
| `makeSimulatorProvingService` | const | — |
| `SubmissionEvent` | const | — |
| `Finalized` | type | — |
| `Submitted` | type | — |
| `InBlock` | type | — |
| `SubmissionError` | class | — |
| `SubmitTransactionMethod` | type | — |
| `SubmitTransactionMethodEffect` | type | — |
| `SubmissionServiceEffect` | interface | — |
| `SubmissionService` | interface | — |
| `DefaultSubmissionConfiguration` | type | — |
| `makeDefaultSubmissionServiceEffect` | const | — |
| `makeDefaultSubmissionService` | const | — |
| `SimulatorSubmissionConfiguration` | type | — |
| `makeSimulatorSubmissionService` | const | — |

#### Sub-export: `./balancer`

| Symbol | Kind | Members |
|--------|------|---------|
| `InsufficientFundsError` | class | — |
| `BalanceRecipe` | interface | — |
| `CoinSelection` | type | — |
| `BalanceRecipeProps` | type | — |
| `getBalanceRecipe` | const | — |
| `createCounterOffer` | const | — |
| `chooseCoin` | const | — |
| `TransactionCostModel` | interface | — |
| `CounterOffer` | class | — |
| `TokenType` | type | — |
| `TokenValue` | type | — |
| `CoinRecipe` | interface | — |
| `Imbalance` | type | — |
| `Imbalances` | type | — |

#### Sub-export: `./submission`

| Symbol | Kind | Members |
|--------|------|---------|
| `SubmissionEvent` | const | — |
| `Finalized` | type | — |
| `Submitted` | type | — |
| `InBlock` | type | — |
| `SubmissionError` | class | — |
| `SubmitTransactionMethod` | type | — |
| `SubmitTransactionMethodEffect` | type | — |
| `SubmissionServiceEffect` | interface | — |
| `SubmissionService` | interface | — |
| `DefaultSubmissionConfiguration` | type | — |
| `makeDefaultSubmissionServiceEffect` | const | — |
| `makeDefaultSubmissionService` | const | — |
| `SimulatorSubmissionConfiguration` | type | — |
| `makeSimulatorSubmissionService` | const | — |

#### Sub-export: `./pendingTransactions`

| Symbol | Kind | Members |
|--------|------|---------|
| `PendingTransactionsService` | type | — |
| `IndexerClientConnection` | type | — |
| `DefaultPendingTransactionsServiceConfiguration` | type | — |
| `InitParams` | type | — |
| `PendingTransactionsServiceImpl` | class | — |
| `PendingTransactionsServiceEffect` | type | — |
| `PendingTransactionsServiceEffectImpl` | class | — |
| `PendingTransactions` | namespace | — |

#### Sub-export: `./proving`

| Symbol | Kind | Members |
|--------|------|---------|
| `ProvingError` | class | — |
| `ProvingServiceEffect` | interface | — |
| `ProvingService` | interface | — |
| `UnboundTransaction` | type | — |
| `fromProvingProviderEffect` | const | — |
| `fromProvingProvider` | const | — |
| `ServerProvingConfiguration` | type | — |
| `WasmProvingConfiguration` | type | — |
| `DefaultProvingConfiguration` | type | — |
| `makeServerProvingServiceEffect` | const | — |
| `makeWasmProvingServiceEffect` | const | — |
| `makeSimulatorProvingServiceEffect` | const | — |
| `makeDefaultProvingServiceEffect` | const | — |
| `makeDefaultProvingService` | const | — |
| `makeServerProvingService` | const | — |
| `makeWasmProvingService` | const | — |
| `makeSimulatorProvingService` | const | — |

### @midnight-ntwrk/wallet-sdk-dust-wallet (3.0.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `DustWalletCapabilities` | type | — |
| `DustWalletState` | class | — |
| `DustWalletAPI` | type | — |
| `DustWallet` | type | — |
| `DustWalletClass` | interface | — |
| `DefaultDustConfiguration` | type | — |

#### Sub-export: `./v1`

| Symbol | Kind | Members |
|--------|------|---------|
| `PublicKey` | type | — |
| `CoreWallet` | type | — |
| `DustWalletCapabilities` | type | — |
| `DustWalletState` | class | — |
| `DustWalletAPI` | type | — |
| `DustWallet` | type | — |
| `DustWalletClass` | interface | — |
| `DefaultDustConfiguration` | type | — |
| `Keys` | namespace | — |
| `Simulator` | namespace | — |
| `SyncService` | namespace | — |
| `Transacting` | namespace | — |
| `Context` | type | — |
| `AnyContext` | type | — |
| `V1Tag` | const | — |
| `DefaultRunningV1` | type | — |
| `RunningV1Variant` | class | — |
| `BaseV1Configuration` | type | — |
| `DefaultV1Configuration` | type | — |
| `DefaultV1Variant` | type | — |
| `V1Variant` | type | — |
| `DefaultV1Builder` | type | — |
| `V1Builder` | class | — |
| `Dust` | type | — |
| `DustWithNullifier` | type | — |
| `DustFullInfo` | type | — |
| `DustGenerationDetails` | type | — |
| `DustGenerationInfo` | type | — |
| `UtxoWithMeta` | type | — |
| `AnyTransaction` | type | — |
| `UnprovenDustSpend` | type | — |
| `NetworkId` | type | — |
| `TotalCostParameters` | type | — |
| `CoinsAndBalances` | namespace | — |

### @midnight-ntwrk/wallet-sdk-facade (3.0.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `FinalizedTransactionRecipe` | type | — |
| `UnboundTransactionRecipe` | type | — |
| `UnprovenTransactionRecipe` | type | — |
| `BalancingRecipe` | type | — |
| `TokenTransfer` | interface | `type`, `receiverAddress`, `amount` |
| `ShieldedTokenTransfer` | type | — |
| `UnshieldedTokenTransfer` | type | — |
| `CombinedTokenTransfer` | type | — |
| `CombinedSwapInputs` | type | — |
| `CombinedSwapOutputs` | type | — |
| `TransactionIdentifier` | type | — |
| `UtxoWithMeta` | type | — |
| `FacadeState` | class | — |
| `TermsAndConditions` | type | — |
| `FetchTermsAndConditionsConfiguration` | type | — |
| `DefaultConfiguration` | type | — |
| `InitParams` | type | — |
| `WalletFacade` | class | — |

### @midnight-ntwrk/wallet-sdk-hd (3.0.1)

| Symbol | Kind | Members |
|--------|------|---------|
| `Roles` | const | — |
| `Role` | type | — |
| `HDWallet` | class | — |
| `AccountKey` | class | — |
| `RoleKey` | class | — |
| `CompositeRoleKey` | class | — |
| `mnemonicToWords` | const | — |
| `generateMnemonicWords` | const | — |
| `joinMnemonicWords` | const | — |
| `generateRandomSeed` | const | — |
| `validateMnemonic` | const | — |

### @midnight-ntwrk/wallet-sdk-indexer-client (1.2.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `Connect` | const | — |
| `Disconnect` | const | — |
| `BlockHash` | const | — |
| `FetchTermsAndConditions` | const | — |
| `TransactionStatus` | const | — |
| `DustLedgerEvents` | const | — |
| `ShieldedTransactions` | const | — |
| `UnshieldedTransactions` | const | — |
| `ZswapEvents` | const | — |
| `Maybe` | type | — |
| `InputMaybe` | type | — |
| `Exact` | type | — |
| `MakeOptional` | type | — |
| `MakeMaybe` | type | — |
| `MakeEmpty` | type | — |
| `Incremental` | type | — |
| `Scalars` | type | — |
| `Block` | type | — |
| `BlockOffset` | type | — |
| `CollapsedMerkleTree` | type | — |
| `CommitteeMember` | type | — |
| `ContractAction` | type | — |
| `ContractActionOffset` | type | — |
| `ContractBalance` | type | — |
| `ContractCall` | type | — |
| `ContractDeploy` | type | — |
| `ContractUpdate` | type | — |
| `DParameter` | type | — |
| `DParameterChange` | type | — |
| `DustGenerationDtimeUpdate` | type | — |
| `DustGenerationStatus` | type | — |
| `DustInitialUtxo` | type | — |
| `DustLedgerEvent` | type | — |
| `DustOutput` | type | — |
| `DustSpendProcessed` | type | — |
| `EpochInfo` | type | — |
| `EpochPerf` | type | — |
| `FirstValidEpoch` | type | — |
| `Mutation` | type | — |
| `MutationConnectArgs` | type | — |
| `MutationDisconnectArgs` | type | — |
| `ParamChange` | type | — |
| `PoolMetadata` | type | — |
| `PresenceEvent` | type | — |
| `Query` | type | — |
| `QueryBlockArgs` | type | — |
| `QueryCommitteeArgs` | type | — |
| `QueryContractActionArgs` | type | — |
| `QueryDustGenerationStatusArgs` | type | — |
| `QueryEpochPerformanceArgs` | type | — |
| `QueryEpochUtilizationArgs` | type | — |
| `QueryPoolMetadataArgs` | type | — |
| `QueryPoolMetadataListArgs` | type | — |
| `QueryRegisteredFirstValidEpochsArgs` | type | — |
| `QueryRegisteredPresenceArgs` | type | — |
| `QueryRegisteredSpoSeriesArgs` | type | — |
| `QueryRegisteredTotalsSeriesArgs` | type | — |
| `QuerySpoByPoolIdArgs` | type | — |
| `QuerySpoCompositeByPoolIdArgs` | type | — |
| `QuerySpoIdentitiesArgs` | type | — |
| `QuerySpoIdentityByPoolIdArgs` | type | — |
| `QuerySpoListArgs` | type | — |
| `QuerySpoPerformanceBySpoSkArgs` | type | — |
| `QuerySpoPerformanceLatestArgs` | type | — |
| `QueryStakeDistributionArgs` | type | — |
| `QueryStakePoolOperatorsArgs` | type | — |
| `QueryTransactionsArgs` | type | — |
| `RegisteredStat` | type | — |
| `RegisteredTotals` | type | — |
| `RegularTransaction` | type | — |
| `RelevantTransaction` | type | — |
| `Segment` | type | — |
| `ShieldedTransactionsEvent` | type | — |
| `ShieldedTransactionsProgress` | type | — |
| `Spo` | type | — |
| `SpoComposite` | type | — |
| `SpoIdentity` | type | — |
| `StakeShare` | type | — |
| `Subscription` | type | — |
| `SubscriptionBlocksArgs` | type | — |
| `SubscriptionContractActionsArgs` | type | — |
| `SubscriptionDustLedgerEventsArgs` | type | — |
| `SubscriptionShieldedTransactionsArgs` | type | — |
| `SubscriptionUnshieldedTransactionsArgs` | type | — |
| `SubscriptionZswapLedgerEventsArgs` | type | — |
| `SystemParameters` | type | — |
| `SystemTransaction` | type | — |
| `TermsAndConditions` | type | — |
| `TermsAndConditionsChange` | type | — |
| `Transaction` | type | — |
| `TransactionFees` | type | — |
| `TransactionOffset` | type | — |
| `TransactionResult` | type | — |
| `TransactionResultStatus` | type | — |
| `UnshieldedTransaction` | type | — |
| `UnshieldedTransactionsEvent` | type | — |
| `UnshieldedTransactionsProgress` | type | — |
| `UnshieldedUtxo` | type | — |
| `ZswapLedgerEvent` | type | — |
| `BlockHashQueryVariables` | type | — |
| `BlockHashQuery` | type | — |
| `ConnectMutationVariables` | type | — |
| `ConnectMutation` | type | — |
| `DisconnectMutationVariables` | type | — |
| `DisconnectMutation` | type | — |
| `FetchTermsAndConditionsQueryVariables` | type | — |
| `FetchTermsAndConditionsQuery` | type | — |
| `TransactionStatusQueryVariables` | type | — |
| `TransactionStatusQuery` | type | — |
| `DustLedgerEventsSubscriptionVariables` | type | — |
| `DustLedgerEventsSubscription` | type | — |
| `ShieldedTransactionsSubscriptionVariables` | type | — |
| `ShieldedTransactionsSubscription` | type | — |
| `UnshieldedTransactionsSubscriptionVariables` | type | — |
| `UnshieldedTransactionsSubscription` | type | — |
| `ZswapEventsSubscriptionVariables` | type | — |
| `ZswapEventsSubscription` | type | — |
| `BlockHashDocument` | const | — |
| `ConnectDocument` | const | — |
| `DisconnectDocument` | const | — |
| `FetchTermsAndConditionsDocument` | const | — |
| `TransactionStatusDocument` | const | — |
| `DustLedgerEventsDocument` | const | — |
| `ShieldedTransactionsDocument` | const | — |
| `UnshieldedTransactionsDocument` | const | — |
| `ZswapEventsDocument` | const | — |

#### Sub-export: `./effect`

| Symbol | Kind | Members |
|--------|------|---------|
| `Query` | namespace | — |
| `Subscription` | namespace | — |
| `QueryClient` | class | — |
| `HttpQueryClient` | namespace | — |
| `SubscriptionClient` | class | — |
| `WsSubscriptionClient` | namespace | — |
| `ConnectionHelper` | namespace | — |
| `QueryRunner` | namespace | — |

### @midnight-ntwrk/wallet-sdk-node-client (1.1.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `Config` | type | — |
| `makeConfig` | const | — |
| `DEFAULT_CONFIG` | const | — |
| `PolkadotNodeClient` | class | — |

#### Sub-export: `./effect`

| Symbol | Kind | Members |
|--------|------|---------|
| `NodeClient` | namespace | — |
| `Config` | type | — |
| `DEFAULT_CONFIG` | const | — |
| `makeConfig` | const | — |
| `PolkadotNodeClient` | class | — |
| `SubmissionEvent` | namespace | — |
| `NodeClientError` | namespace | — |

#### Sub-export: `./testing`

| Symbol | Kind | Members |
|--------|------|---------|
| `TestTransactions` | namespace | — |

### @midnight-ntwrk/wallet-sdk-prover-client (1.2.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `HttpProverClient` | class | — |

#### Sub-export: `./effect`

| Symbol | Kind | Members |
|--------|------|---------|
| `ProverClient` | namespace | — |
| `HttpProverClient` | namespace | — |
| `WasmProver` | namespace | — |

### @midnight-ntwrk/wallet-sdk-runtime (1.0.2)

| Symbol | Kind | Members |
|--------|------|---------|
| `WalletBuilder` | class | — |
| `BuildArguments` | type | — |
| `FullConfiguration` | type | — |
| `Runtime` | namespace | — |

#### Sub-export: `./abstractions`

| Symbol | Kind | Members |
|--------|------|---------|
| `Variant` | namespace | — |
| `VariantBuilder` | namespace | — |
| `WalletLike` | namespace | — |
| `WalletRuntimeError` | class | — |
| `StateChange` | namespace | — |
| `VersionChangeType` | namespace | — |

### @midnight-ntwrk/wallet-sdk-shielded (2.1.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `ShieldedWalletCapabilities` | type | — |
| `UnboundTransaction` | type | — |
| `ShieldedWalletState` | class | — |
| `ShieldedWallet` | type | — |
| `ShieldedWalletClass` | type | — |
| `ShieldedWalletAPI` | type | — |
| `CustomizedShieldedWallet` | type | — |
| `DefaultShieldedConfiguration` | type | — |
| `CustomizedShieldedWalletClass` | interface | — |
| `CustomShieldedWallet` | function | — |

#### Sub-export: `./v1`

| Symbol | Kind | Members |
|--------|------|---------|
| `BaseV1Configuration` | type | — |
| `DefaultV1Configuration` | type | — |
| `V1Variant` | type | — |
| `AnyV1Variant` | type | — |
| `DefaultV1Variant` | type | — |
| `TransactionOf` | type | — |
| `AuxDataOf` | type | — |
| `SerializedStateOf` | type | — |
| `DefaultV1Builder` | type | — |
| `V1Builder` | class | — |
| `Sync` | namespace | — |
| `Transacting` | namespace | — |
| `TransactionHistory` | namespace | — |
| `Serialization` | namespace | — |
| `CoinsAndBalances` | namespace | — |
| `Keys` | namespace | — |
| `Context` | type | — |
| `AnyContext` | type | — |
| `V1Tag` | const | — |
| `DefaultRunningV1` | type | — |
| `RunningV1Variant` | class | — |
| `Simulator` | namespace | — |
| `WalletError` | namespace | — |
| `PublicKeys` | type | — |
| `CoinHashesMap` | type | — |
| `CoreWallet` | type | — |
| `TransactionOps` | type | — |

### @midnight-ntwrk/wallet-sdk-unshielded-wallet (2.1.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `UnshieldedWalletCapabilities` | type | — |
| `UnshieldedWalletState` | class | — |
| `UnshieldedWallet` | type | — |
| `DefaultUnshieldedConfiguration` | type | — |
| `UnshieldedWalletClass` | type | — |
| `UnshieldedWalletAPI` | type | — |
| `CustomizedUnshieldedWallet` | type | — |
| `CustomizedUnshieldedWalletClass` | interface | — |
| `CustomUnshieldedWallet` | function | — |
| `TransactionHistory` | type | — |
| `InMemoryTransactionHistoryStorage` | class | — |
| `NoOpTransactionHistoryStorage` | class | — |
| `TransactionHash` | type | — |
| `TransactionHistoryEntrySchema` | const | — |
| `TransactionHistoryEntry` | type | — |
| `TransactionHistoryStorage` | interface | — |
| `PublicKey` | type | — |
| `UnshieldedKeystore` | interface | — |
| `createKeystore` | const | — |

#### Sub-export: `./v1`

| Symbol | Kind | Members |
|--------|------|---------|
| `BaseV1Configuration` | type | — |
| `DefaultV1Configuration` | type | — |
| `V1Variant` | type | — |
| `AnyV1Variant` | type | — |
| `DefaultV1Variant` | type | — |
| `TransactionOf` | type | — |
| `SerializedStateOf` | type | — |
| `DefaultV1Builder` | type | — |
| `V1Builder` | class | — |
| `Sync` | namespace | — |
| `SyncProgress` | namespace | — |
| `Transacting` | namespace | — |
| `TransactionHistory` | namespace | — |
| `Serialization` | namespace | — |
| `CoinsAndBalances` | namespace | — |
| `Keys` | namespace | — |
| `Context` | type | — |
| `AnyContext` | type | — |
| `V1Tag` | const | — |
| `DefaultRunningV1` | type | — |
| `RunningV1Variant` | class | — |
| `Simulator` | namespace | — |
| `WalletError` | namespace | — |
| `CoreWallet` | type | — |
| `UnboundTransaction` | type | — |
| `IntentOf` | type | — |
| `TransactionOps` | type | — |
| `TransactionHistoryStorage` | namespace | — |
| `UnshieldedState` | namespace | — |

### @midnight-ntwrk/wallet-sdk-utilities (1.1.0)

| Symbol | Kind | Members |
|--------|------|---------|
| `ArrayOps` | namespace | — |
| `BlobOps` | namespace | — |
| `DateOps` | namespace | — |
| `EitherOps` | namespace | — |
| `Fluent` | namespace | — |
| `HList` | namespace | — |
| `LedgerOps` | namespace | — |
| `ObservableOps` | namespace | — |
| `Poly` | namespace | — |
| `RecordOps` | namespace | — |
| `SafeBigInt` | namespace | — |

#### Sub-export: `./networking`

| Symbol | Kind | Members |
|--------|------|---------|
| `HttpURL` | namespace | — |
| `WsURL` | namespace | — |
| `InvalidProtocolSchemeError` | class | — |
| `FailedToDeriveWebSocketUrlError` | class | — |
| `URLError` | type | — |
| `ClientError` | class | — |
| `ServerError` | class | — |

#### Sub-export: `./types`

| Symbol | Kind | Members |
|--------|------|---------|
| `CanAssign` | type | — |
| `Expect` | type | — |
| `ItemType` | type | — |
| `Equal` | type | — |

#### Sub-export: `./testing`

| Symbol | Kind | Members |
|--------|------|---------|
| `TestContainers` | namespace | — |
| `getRepositoryRoot` | function | — |
| `getComposeDirectory` | function | — |
| `BuildTestEnvironmentVariablesOptions` | interface | — |
| `buildTestEnvironmentVariables` | function | — |

**Total: 471 symbols, 3 members across 13 packages**

---

## 3. Foundation Layer

### 3.1 Abstractions (`@midnight-ntwrk/wallet-sdk-abstractions`)

Branded types that form the domain vocabulary of the wallet SDK. Each
type is a nominal brand using Effect's `Brand` module, preventing
accidental use of raw primitives where a branded type is expected.

| Export | Kind | Description |
|--------|------|-------------|
| `WalletSeed` | namespace | Branded `Uint8Array` for BIP32 seeds. Has `fromString(hex)`, `is(value)` predicate. |
| `WalletState` | namespace | Branded `string` for serialized wallet state. Has `is(value)` predicate. |
| `SerializedTransaction` | namespace | Branded `Uint8Array` for serialized transactions. Has `of(bytes)` and `from(serializable)` helpers. |
| `ProtocolVersion` | namespace | Branded `bigint` for protocol versions. Has `makeRange`, `withinRange`, `is`, `MinSupportedVersion` (0), `MaxSupportedVersion`. |
| `NetworkId` | namespace | Well-known network identifiers: `MainNet`, `TestNet`, `DevNet`, `QaNet`, `Undeployed`, `Preview`, `PreProd`. Accepts any string. |
| `SyncProgress` | namespace | Tracks wallet sync state. `createSyncProgress(params)` creates instances with `isStrictlyComplete()` and `isCompleteWithin(maxGap)` methods. |
| `ProtocolState` | namespace | Wraps wallet state with protocol version metadata. |

| Test | Status | Detail |
|------|--------|--------|
| abstractions/WalletSeed-from-bytes | PASS | — |
| abstractions/WalletSeed-from-string | PASS | — |
| abstractions/WalletSeed-is-predicate | PASS | — |
| abstractions/WalletState-from-string | PASS | — |
| abstractions/SerializedTransaction-of | PASS | — |
| abstractions/ProtocolVersion-construction | PASS | — |
| abstractions/ProtocolVersion-min-max | PASS | — |
| abstractions/ProtocolVersion-range-within | PASS | — |
| abstractions/ProtocolVersion-is-predicate | PASS | — |
| abstractions/NetworkId-well-known | PASS | — |
| abstractions/SyncProgress-create | PASS | — |
| abstractions/SyncProgress-strictly-complete | PASS | — |
| abstractions/SyncProgress-not-complete | PASS | — |

### 3.2 Utilities (`@midnight-ntwrk/wallet-sdk-utilities`)

Domain-agnostic operations used across the SDK. Each utility is exported
as a namespace module containing related functions.

| Export | Kind | Description |
|--------|------|-------------|
| `SafeBigInt` | namespace | Safe conversions between bigint and other types. |
| `ArrayOps` | namespace | Array utility functions (filter, map, group). |
| `BlobOps` | namespace | Binary/blob operations (concat, slice, compare). |
| `DateOps` | namespace | Date arithmetic (e.g., `addSeconds`). |
| `EitherOps` | namespace | Effect Either utilities. |
| `Fluent` | namespace | Fluent API builder helpers. |
| `HList` | namespace | Heterogeneous list types (used by WalletBuilder). |
| `LedgerOps` | namespace | Ledger-specific type operations. |
| `ObservableOps` | namespace | RxJS Observable utilities. |
| `Poly` | namespace | Polymorphic function utilities (variant dispatch). |
| `RecordOps` | namespace | Record/object utilities. |

**Sub-export `./networking`**: URL validation types and HTTP error classes.

| Export | Kind | Description |
|--------|------|-------------|
| `HttpURL` | namespace | Validated HTTP URL type. |
| `WsURL` | namespace | Validated WebSocket URL type. |
| `InvalidProtocolSchemeError` | class | Tagged error (`_tag: 'InvalidProtocolSchemeError'`) for invalid URL schemes. |
| `FailedToDeriveWebSocketUrlError` | class | Tagged error for WebSocket URL derivation failures. |
| `ClientError` | class | Tagged error (`_tag: 'ClientError'`) for HTTP 4xx responses. |
| `ServerError` | class | Tagged error (`_tag: 'ServerError'`) for HTTP 5xx responses. |

**Sub-export `./types`**: Compile-time type utilities (`Expect`, `Equal`, `CanAssign`, `ItemType`).

| Test | Status | Detail |
|------|--------|--------|
| utilities/SafeBigInt-namespace-exists | PASS | — |
| utilities/DateOps-namespace-exists | PASS | — |
| utilities/ArrayOps-namespace-exists | PASS | — |
| utilities/BlobOps-namespace-exists | PASS | — |
| utilities/InvalidProtocolSchemeError-construction | PASS | — |
| utilities/FailedToDeriveWebSocketUrlError-construction | PASS | — |
| utilities/ClientError-construction | PASS | — |
| utilities/ServerError-construction | PASS | — |

### 3.3 HD Wallet (`@midnight-ntwrk/wallet-sdk-hd`)

Hierarchical deterministic key derivation following BIP32/BIP39 with
Midnight's derivation path: `m/44'/2400'/{account}'/{role}/{index}`.

Coin type `2400` is Midnight's registered BIP44 coin type. Five roles
partition keys by purpose:

| Role | Index | Purpose |
|------|-------|---------|
| NightExternal | 0 | Receiving Night tokens |
| NightInternal | 1 | Change addresses for Night |
| Dust | 2 | Dust generation addresses |
| Zswap | 3 | Zero-knowledge swap keys |
| Metadata | 4 | Metadata encryption keys |

| Export | Kind | Description |
|--------|------|-------------|
| `HDWallet` | class | HD wallet root. `fromSeed(seed)` returns `{ type: 'seedOk', hdWallet }` or `{ type: 'seedError', error }`. Call `selectAccount(n)` to get an `AccountKey`. |
| `AccountKey` | class | Account-level key. `selectRole(role)` gets a `RoleKey`; `selectRoles([...])` gets a `CompositeRoleKey` for batch derivation. |
| `RoleKey` | class | Role-level key. `deriveKeyAt(index)` returns `{ type: 'keyDerived', key: Uint8Array }` or `{ type: 'keyOutOfBounds' }`. |
| `CompositeRoleKey` | class | Multi-role key. `deriveKeysAt(index)` derives keys for all specified roles simultaneously. |
| `Roles` | const | Enum object: `{ NightExternal: 0, NightInternal: 1, Dust: 2, Zswap: 3, Metadata: 4 }`. |
| `generateMnemonicWords` | function | Generates a 24-word BIP39 mnemonic (default strength=256). |
| `joinMnemonicWords` | function | Joins word array into space-separated mnemonic string. |
| `mnemonicToWords` | function | Splits mnemonic string into word array. |
| `validateMnemonic` | function | Validates a BIP39 mnemonic against the English wordlist. |
| `generateRandomSeed` | function | Generates a cryptographically random 32-byte seed. |

Key derivation is **deterministic**: the same seed always produces the
same keys. Different roles produce different keys. Different accounts
produce different keys.

| Test | Status | Detail |
|------|--------|--------|
| hd/mnemonic-generates-24-words | PASS | — |
| hd/mnemonic-validates-roundtrip | PASS | — |
| hd/invalid-mnemonic-rejected | PASS | — |
| hd/random-seed-length | PASS | — |
| hd/wallet-from-seed | PASS | — |
| hd/roles-values | PASS | — |
| hd/derive-key-at-index-0 | PASS | — |
| hd/different-roles-different-keys | PASS | — |
| hd/different-accounts-different-keys | PASS | — |
| hd/composite-role-keys | PASS | — |
| hd/deterministic-derivation | PASS | — |

### 3.4 Address Format (`@midnight-ntwrk/wallet-sdk-address-format`)

Bech32m address encoding with the `mn` prefix. Addresses encode a type
identifier, network identifier, and payload into a single checksummed
string.

| Address Type | Bech32m Type | Payload | Purpose |
|-------------|-------------|---------|---------|
| `ShieldedAddress` | `shield-addr` | Coin public key + encryption public key | Receiving shielded tokens |
| `UnshieldedAddress` | `addr` | 32-byte public key | Receiving Night tokens |
| `DustAddress` | `dust` | BLS scalar (bigint < modulus) | Dust generation registration |
| `ShieldedCoinPublicKey` | `shield-cpk` | 32 bytes | Proving coin ownership |
| `ShieldedEncryptionPublicKey` | `shield-epk` | 32 bytes | Encrypting messages |
| `ShieldedEncryptionSecretKey` | `shield-esk` | 32 bytes | Decrypting messages |

| Export | Kind | Description |
|--------|------|-------------|
| `MidnightBech32m` | class | Encodes/decodes addresses. `encode(networkId, item)` produces a Bech32m string; `parse(str)` + `decode(Type, networkId)` reverses it. |
| `Bech32mCodec` | class | Generic codec for custom Bech32m types. |
| `BLSScalar` | const | BLS12-381 scalar field: `{ bytes: 32, modulus: 0x73eda753...01 }`. |
| `ScaleBigInt` | const | SCALE codec for bigint encoding/decoding. Roundtrips correctly for all non-negative values. |
| `ShieldedAddress` | class | Composed of `coinPublicKey` + `encryptionPublicKey`. |
| `ShieldedCoinPublicKey` | class | 32-byte key with `toHexString()`, `equals()`, `fromHexString()`. **Accepts `Buffer`, not `Uint8Array`.** |
| `ShieldedEncryptionPublicKey` | class | 32-byte key with `toHexString()`, `equals()`. **Accepts `Buffer`.** |
| `UnshieldedAddress` | class | 32-byte address with `hexString` getter and `equals()`. **Accepts `Buffer`.** |
| `DustAddress` | class | Stores a `bigint` validated against `BLSScalar.modulus`. Values `>= modulus` are rejected. |
| `mainnet` | symbol | Unique symbol for mainnet network ID (not a string). |

**Important:** Address constructors accept `Buffer`, not `Uint8Array`.
This is a platform-specific choice aligned with Node.js conventions.

| Test | Status | Detail |
|------|--------|--------|
| address-format/BLSScalar-is-32-bytes | PASS | — |
| address-format/ScaleBigInt-encode-decode-roundtrip | PASS | — |
| address-format/ScaleBigInt-zero | PASS | — |
| address-format/ScaleBigInt-large-value | PASS | — |
| address-format/ShieldedCoinPublicKey-construction | PASS | — |
| address-format/ShieldedCoinPublicKey-equality | PASS | — |
| address-format/ShieldedEncryptionPublicKey-construction | PASS | — |
| address-format/ShieldedAddress-composition | PASS | — |
| address-format/UnshieldedAddress-hex-roundtrip | PASS | — |
| address-format/UnshieldedAddress-equality | PASS | — |
| address-format/DustAddress-from-bigint | PASS | — |
| address-format/DustAddress-rejects-over-modulus | PASS | — |
| address-format/DustAddress-validates-modulus | PASS | — |
| address-format/MidnightBech32m-encode-shielded | PASS | — |
| address-format/MidnightBech32m-encode-unshielded | PASS | — |
| address-format/MidnightBech32m-roundtrip | PASS | — |

---

## 4. Infrastructure Clients

### 4.1 Node Client (`@midnight-ntwrk/wallet-sdk-node-client`)

Polkadot RPC client for submitting transactions to the Midnight node.
Uses the `@polkadot/api` library over WebSocket.

| Export | Kind | Description |
|--------|------|-------------|
| `PolkadotNodeClient` | class | Main client. `init(config)` connects to the node. `sendMidnightTransaction(tx)` returns an `Observable<SubmissionEvent>` tracking the submission lifecycle. `close()` disconnects. |
| `Config` | type | `{ nodeURL: URL, reconnectionTimeout: Duration, reconnectionDelay: Duration }`. |
| `makeConfig` | function | Creates a `Config` from partial input. Only `nodeURL` is required; timeouts have defaults. |
| `DEFAULT_CONFIG` | const | Default reconnection settings (infinite timeout, 1s delay). Does not include `nodeURL`. |

The submission event stream emits `Submitted`, `InBlock`, and `Finalized`
events as the transaction progresses through the block pipeline.

*No evidence collected yet.*

### 4.2 Indexer Client (`@midnight-ntwrk/wallet-sdk-indexer-client`)

GraphQL client for querying the chain indexer. The indexer tracks wallet-relevant
events (UTxO creation/spending, shielded transactions, dust generation).

**Queries:**

| Query | Parameters | Purpose |
|-------|-----------|---------|
| `Connect` | `viewingKey: ViewingKey` | Register a viewing key to start a session |
| `Disconnect` | `sessionId: HexEncoded` | End an indexer session |
| `BlockHash` | `offset?: BlockOffset` | Get block height, hash, ledger params, timestamp |
| `TransactionStatus` | `transactionId: HexEncoded` | Check transaction result and segment status |

**Subscriptions:**

| Subscription | Purpose |
|-------------|---------|
| `DustLedgerEvents` | Stream dust ledger events for sync |
| `ShieldedTransactions` | Stream shielded transactions with Merkle tree updates |
| `UnshieldedTransactions` | Stream unshielded UTxO events |
| `ZswapEvents` | Stream Zswap ledger events |

**Effect sub-export (`./effect`):**

| Export | Kind | Description |
|--------|------|-------------|
| `QueryClient` | tag | Effect Context Tag for GraphQL query execution. |
| `SubscriptionClient` | tag | Effect Context Tag for GraphQL subscription streaming. |
| `HttpQueryClient` | namespace | `layer(config)` creates an Effect Layer for HTTP queries. |
| `WsSubscriptionClient` | namespace | `layer(config)` creates an Effect Layer for WebSocket subscriptions. |
| `ConnectionHelper` | namespace | `deriveWebSocketUrl(url)` converts HTTP to WS URL. |
| `QueryRunner` | namespace | `runPromise(query, vars, config)` executes a query as a plain Promise. |

**Note:** This package also exports ~120 auto-generated GraphQL types
from the indexer schema (see Appendix A).

| Test | Status | Detail |
|------|--------|--------|
| indexer-client/Connect-query-exists | PASS | — |
| indexer-client/Disconnect-query-exists | PASS | — |
| indexer-client/BlockHash-query-exists | PASS | — |
| indexer-client/TransactionStatus-query-exists | PASS | — |
| indexer-client/QueryClient-is-context-tag | PASS | — |
| indexer-client/SubscriptionClient-is-context-tag | PASS | — |
| indexer-client/ConnectionHelper-deriveWebSocketUrl | PASS | — |
| indexer-client/QueryRunner-runPromise | PASS | — |

### 4.3 Prover Client (`@midnight-ntwrk/wallet-sdk-prover-client`)

HTTP client for the ZK proof generation server. The proof server converts
`UnprovenTransaction` objects into `Transaction` objects with valid ZK proofs.

| Export | Kind | Description |
|--------|------|-------------|
| `HttpProverClient` | class | Wraps an HTTP connection. Constructor takes `{ url: string }`. `proveTransaction(tx, costModel?)` sends the transaction for proving and returns the proven version. |

The Effect sub-export (`./effect`) provides `ProverClient` (Context Tag),
`HttpProverClient` layer, and `SimulatorProverClient` for testing.

*No evidence collected yet.*

---

## 5. Capabilities

Shared capability implementations decomposed into pure state operations
(capabilities) and side-effecting services. Every service has both an
Effect-based variant (`*Effect`) and a plain Promise-based variant.

### 5.1 Balancer (`@midnight-ntwrk/wallet-sdk-capabilities/balancer`)

Coin selection and transaction balancing. Computes which UTxOs to consume
and what change outputs to create to satisfy a set of imbalances.

| Export | Kind | Description |
|--------|------|-------------|
| `getBalanceRecipe` | function | Core balancing algorithm. Takes coins, imbalances, cost model, and coin selection strategy; returns a `BalanceRecipe` with inputs and outputs. |
| `createCounterOffer` | function | Creates a `CounterOffer` for multi-party transaction negotiation. |
| `chooseCoin` | function | Default coin selection: picks the smallest-value coin of the requested type. |
| `BalanceRecipe` | interface | `{ inputs: TInput[], outputs: TOutput[] }` — the result of balancing. |
| `CoinRecipe` | interface | `{ type: TokenType, value: TokenValue }` — a coin's type and value. |
| `TransactionCostModel` | interface | `{ inputFeeOverhead: bigint, outputFeeOverhead: bigint }` — per-input/output fee constants. |
| `CounterOffer` | class | Mutable accumulator for multi-step balancing with `addInput`/`addOutput`. |
| `Imbalances` | type/const | `Map<TokenType, TokenValue>` with companion methods: `empty()`, `fromEntry()`, `merge()`, `getValue()`, `typeSet()`. |
| `InsufficientFundsError` | class | Thrown when balancing cannot satisfy an imbalance. Has `tokenType` property. |

*No evidence collected yet.*

### 5.2 Submission (`@midnight-ntwrk/wallet-sdk-capabilities/submission`)

Transaction submission lifecycle: submit → in-block → finalized.

| Export | Kind | Description |
|--------|------|-------------|
| `SubmissionService` | interface | `prove(tx): Promise<T>` — submits a transaction. |
| `SubmissionServiceEffect` | interface | Effect-based variant. |
| `SubmissionEvent` | const/type | Tagged union of `Submitted`, `InBlock`, `Finalized` events (from `Data.taggedEnum`). |
| `SubmissionError` | class | Tagged error (`_tag: 'SubmissionError'`) with `{ message, cause? }`. |
| `makeDefaultSubmissionService` | function | Creates a submission service from a `DefaultSubmissionConfiguration` (`{ relayURL: URL }`). |
| `makeSimulatorSubmissionService` | function | Creates a mock submission service for testing. |

*No evidence collected yet.*

### 5.3 Pending Transactions (`@midnight-ntwrk/wallet-sdk-capabilities/pendingTransactions`)

Tracks transactions that have been submitted but not yet finalized.
Monitors the indexer for confirmation and expiry.

| Export | Kind | Description |
|--------|------|-------------|
| `PendingTransactionsService` | type | Service interface for tracking pending transactions. |
| `PendingTransactionsServiceImpl` | class | Promise-based implementation. |
| `PendingTransactions` | namespace | Contains `TransactionTrait` interface for transaction identity operations (serialize, deserialize, ID extraction, TTL checking). |

*No evidence collected yet.*

### 5.4 Proving (`@midnight-ntwrk/wallet-sdk-capabilities/proving`)

ZK proof generation services. Multiple backends for different environments.

| Export | Kind | Description |
|--------|------|-------------|
| `ProvingService` | interface | `prove(tx: UnprovenTransaction): Promise<T>` — generates a ZK proof for a transaction. |
| `ProvingServiceEffect` | interface | Effect-based variant. |
| `ProvingError` | class | Tagged error (`_tag: 'Wallet.Proving'`) with **required** `cause: Error`. |
| `makeDefaultProvingService` | function | Creates a server-backed proving service (requires `{ provingServerUrl: URL }`). |
| `makeServerProvingService` | function | Explicit server-backed proving service. |
| `makeWasmProvingService` | function | In-browser WASM proving (optional `keyMaterialProvider`). |
| `makeSimulatorProvingService` | function | Mock proving for testing — returns `ProofErasedTransaction` (no real proof). |
| `UnboundTransaction` | type | A transaction with a real proof but pre-binding. |

*No evidence collected yet.*

---

## 6. Wallet Implementations

All three wallets follow the same structural pattern:

1. A **factory function** (`ShieldedWallet(config)`) returns a wallet class
2. The class has `startWithSeed`/`startWithSecretKeys`/`restore` static methods
3. Each instance exposes `state: Observable<WalletState>`, lifecycle methods
   (`start`, `stop`), and token-specific operations
4. The `WalletState` class wraps capabilities (serialization, coins/balances,
   keys, transaction history) and provides convenient getters

### 6.1 Shielded Wallet (`@midnight-ntwrk/wallet-sdk-shielded`)

Privacy-preserving wallet for custom shielded tokens. Uses zero-knowledge
proofs for all operations — balances and transaction details are hidden
from observers.

| Export | Kind | Description |
|--------|------|-------------|
| `ShieldedWallet` | function | Factory: `ShieldedWallet(config) → ShieldedWalletClass`. |
| `ShieldedWalletClass` | type | Class with `startWithSeed(seed)`, `startWithSecretKeys(keys)`, `restore(state)`. |
| `ShieldedWalletState` | class | State with getters: `balances` (record of token balances), `totalCoins`, `availableCoins`, `pendingCoins`, `coinPublicKey`, `encryptionPublicKey`, `address`, `progress`. |
| `ShieldedWalletAPI` | type | Instance API: `state`, `start(secretKeys)`, `balanceTransaction(keys, tx)`, `transferTransaction(keys, outputs)`, `initSwap(keys, inputs, outputs)`, `getAddress()`, `revertTransaction(tx)`, `stop()`. |
| `DefaultShieldedConfiguration` | type | Default configuration type (aliases `DefaultV1Configuration`). |

The shielded wallet requires `ZswapSecretKeys` for most operations,
ensuring that only the key holder can view or modify the wallet state.

*No evidence collected yet.*

### 6.2 Unshielded Wallet (`@midnight-ntwrk/wallet-sdk-unshielded-wallet`)

Wallet for Night (the native token) and other unshielded tokens.
Operations are publicly visible on the ledger.

| Export | Kind | Description |
|--------|------|-------------|
| `UnshieldedWallet` | function | Factory: `UnshieldedWallet(config) → UnshieldedWalletClass`. |
| `UnshieldedWalletClass` | type | Class with `startWithPublicKey(pk)`, `restore(state)`. |
| `UnshieldedWalletState` | class | State with getters: `balances`, `totalCoins`, `availableCoins`, `pendingCoins`, `address`, `progress`, `transactionHistory`. |
| `UnshieldedWalletAPI` | type | Instance API with three balance methods: `balanceFinalizedTransaction`, `balanceUnboundTransaction`, `balanceUnprovenTransaction`. Also `transferTransaction(outputs, ttl)`, `initSwap(inputs, outputs, ttl)`, signing methods. |
| `createKeystore` | function | `createKeystore(secretKey: Uint8Array, networkId): UnshieldedKeystore` — creates a keystore from a derived secret key. |
| `PublicKey` | type/const | `{ publicKey, addressHex, address }` with `fromKeyStore(keystore)` factory. |
| `TransactionHistoryStorage` | interface | `create`, `delete`, `getAll`, `get` for transaction history persistence. |

Unlike the shielded wallet, the unshielded wallet uses public-key
based authentication and provides separate balancing methods for each
transaction stage.

*No evidence collected yet.*

### 6.3 Dust Wallet (`@midnight-ntwrk/wallet-sdk-dust-wallet`)

Fee management wallet. Dust is generated over time from registered Night
UTxOs. Each dust coin has generation parameters:

- **`backingNight`**: The Night UTxO backing this dust generation
- **`rate`**: Dust generation rate (dust per unit time)
- **`maxCap`**: Maximum dust that can accumulate
- **`ctime`**: Creation time of the dust generation

The balance at any given time is computed from these parameters, making
it **time-dependent**: `balance(date)` returns the current dust amount
as a `bigint` (in SPECK, the smallest dust unit).

| Export | Kind | Description |
|--------|------|-------------|
| `DustWallet` | function | Factory: `DustWallet(config) → DustWalletClass`. |
| `DustWalletClass` | type | Class with `startWithSeed(seed, dustParams)`, `startWithSecretKey(key, dustParams)`, `restore(state)`. |
| `DustWalletState` | class | State with getters: `address`, `totalCoins`, `availableCoins`, `pendingCoins`, `progress`, and methods: `balance(time)` (returns `bigint`), `availableCoinsWithFullInfo(time)`, `estimateDustGeneration(nightUtxos, time)`. |
| `DustWalletAPI` | type | Instance API: `state`, `start(secretKey)`, `createDustGenerationTransaction(...)`, `calculateFee(txs)`, `estimateFee(...)`, `balanceTransactions(...)`, `getAddress()`, `stop()`. |
| `DefaultDustConfiguration` | type | Default configuration type. |

*No evidence collected yet.*

---

## 7. Facade (`@midnight-ntwrk/wallet-sdk-facade`)

The unified entry point. Orchestrates all three wallet types for
transaction balancing — running unshielded, shielded, and dust/fee
balancing in sequence, then merging results.

| Export | Kind | Description |
|--------|------|-------------|
| `WalletFacade` | class | Main facade. `init(params)` creates an instance. Exposes `state()` (Observable), `submitTransaction`, three `balance*` methods, `transferTransaction`, `initSwap`, dust registration/deregistration, and lifecycle methods. |
| `FacadeState` | class | Combined state: `shielded`, `unshielded`, `dust` sub-states plus `pending`. `isSynced` getter checks all three are synced. |
| `BalancingRecipe` | type/const | Union of `FinalizedTransactionRecipe`, `UnboundTransactionRecipe`, `UnprovenTransactionRecipe`. `isRecipe(value)` type guard and `getTransactions(recipe)` extractor. |
| `TokenTransfer` | type | `{ type: RawTokenType, receiverAddress: AddressType, amount: bigint }`. |
| `ShieldedTokenTransfer` | type | Transfer targeting shielded addresses. |
| `UnshieldedTokenTransfer` | type | Transfer targeting unshielded addresses. |
| `CombinedTokenTransfer` | type | Union of shielded and unshielded transfers. |
| `CombinedSwapInputs` | type | Input specification for atomic swaps across token types. |
| `DefaultConfiguration` | type | Intersection of all sub-wallet and service configurations. |

**Note:** `TokenKind` (`'dust' | 'shielded' | 'unshielded'`) and
`TokenKindsToBalance` are used internally but are **not exported**.

The facade exposes its sub-wallets as public readonly fields: `shielded`,
`unshielded`, `dust`, `submissionService`, `pendingTransactionsService`,
`provingService`.

*No evidence collected yet.*

---

## 8. Runtime (`@midnight-ntwrk/wallet-sdk-runtime`)

Wallet builder and lifecycle management. The runtime infrastructure
enables seamless hard-fork migration by dispatching wallet operations
to the correct variant based on the current protocol version.

| Export | Kind | Description |
|--------|------|-------------|
| `WalletBuilder` | class | `init()` creates a builder. `withVariant(sinceVersion, variantBuilder)` registers a variant. `build(config?)` produces a wallet class. |
| `Runtime` | namespace | `initHead(args)` starts the runtime with the first variant. `init(args)` starts with a specific variant. `dispatch(runtime, impl)` invokes a polymorphic operation on the active variant. |

**Sub-export `./abstractions`:**

| Export | Kind | Description |
|--------|------|-------------|
| `Variant` | namespace | Variant type definitions and type-level utilities. |
| `VariantBuilder` | namespace | Builder types for constructing variants. |
| `WalletLike` | namespace | Base wallet interface that all variants implement. |
| `WalletRuntimeError` | class | Tagged error (`_tag: 'WalletRuntimeError'`) with `{ message, cause? }`. |
| `StateChange` | namespace | State transition types for version migration. |
| `VersionChangeType` | namespace | Protocol version change classification. |

*No evidence collected yet.*

---

## Appendix A: Generated GraphQL Types (indexer-client)

The indexer-client package exports ~120 auto-generated types from the
indexer's GraphQL schema. These are listed here for completeness.

| Symbol | Kind |
|--------|------|
| `Connect` | const |
| `Disconnect` | const |
| `BlockHash` | const |
| `FetchTermsAndConditions` | const |
| `TransactionStatus` | const |
| `DustLedgerEvents` | const |
| `ShieldedTransactions` | const |
| `UnshieldedTransactions` | const |
| `ZswapEvents` | const |
| `Maybe` | type |
| `InputMaybe` | type |
| `Exact` | type |
| `MakeOptional` | type |
| `MakeMaybe` | type |
| `MakeEmpty` | type |
| `Incremental` | type |
| `Scalars` | type |
| `Block` | type |
| `BlockOffset` | type |
| `CollapsedMerkleTree` | type |
| `CommitteeMember` | type |
| `ContractAction` | type |
| `ContractActionOffset` | type |
| `ContractBalance` | type |
| `ContractCall` | type |
| `ContractDeploy` | type |
| `ContractUpdate` | type |
| `DParameter` | type |
| `DParameterChange` | type |
| `DustGenerationDtimeUpdate` | type |
| `DustGenerationStatus` | type |
| `DustInitialUtxo` | type |
| `DustLedgerEvent` | type |
| `DustOutput` | type |
| `DustSpendProcessed` | type |
| `EpochInfo` | type |
| `EpochPerf` | type |
| `FirstValidEpoch` | type |
| `Mutation` | type |
| `MutationConnectArgs` | type |
| `MutationDisconnectArgs` | type |
| `ParamChange` | type |
| `PoolMetadata` | type |
| `PresenceEvent` | type |
| `Query` | type |
| `QueryBlockArgs` | type |
| `QueryCommitteeArgs` | type |
| `QueryContractActionArgs` | type |
| `QueryDustGenerationStatusArgs` | type |
| `QueryEpochPerformanceArgs` | type |
| `QueryEpochUtilizationArgs` | type |
| `QueryPoolMetadataArgs` | type |
| `QueryPoolMetadataListArgs` | type |
| `QueryRegisteredFirstValidEpochsArgs` | type |
| `QueryRegisteredPresenceArgs` | type |
| `QueryRegisteredSpoSeriesArgs` | type |
| `QueryRegisteredTotalsSeriesArgs` | type |
| `QuerySpoByPoolIdArgs` | type |
| `QuerySpoCompositeByPoolIdArgs` | type |
| `QuerySpoIdentitiesArgs` | type |
| `QuerySpoIdentityByPoolIdArgs` | type |
| `QuerySpoListArgs` | type |
| `QuerySpoPerformanceBySpoSkArgs` | type |
| `QuerySpoPerformanceLatestArgs` | type |
| `QueryStakeDistributionArgs` | type |
| `QueryStakePoolOperatorsArgs` | type |
| `QueryTransactionsArgs` | type |
| `RegisteredStat` | type |
| `RegisteredTotals` | type |
| `RegularTransaction` | type |
| `RelevantTransaction` | type |
| `Segment` | type |
| `ShieldedTransactionsEvent` | type |
| `ShieldedTransactionsProgress` | type |
| `Spo` | type |
| `SpoComposite` | type |
| `SpoIdentity` | type |
| `StakeShare` | type |
| `Subscription` | type |
| `SubscriptionBlocksArgs` | type |
| `SubscriptionContractActionsArgs` | type |
| `SubscriptionDustLedgerEventsArgs` | type |
| `SubscriptionShieldedTransactionsArgs` | type |
| `SubscriptionUnshieldedTransactionsArgs` | type |
| `SubscriptionZswapLedgerEventsArgs` | type |
| `SystemParameters` | type |
| `SystemTransaction` | type |
| `TermsAndConditions` | type |
| `TermsAndConditionsChange` | type |
| `Transaction` | type |
| `TransactionFees` | type |
| `TransactionOffset` | type |
| `TransactionResult` | type |
| `TransactionResultStatus` | type |
| `UnshieldedTransaction` | type |
| `UnshieldedTransactionsEvent` | type |
| `UnshieldedTransactionsProgress` | type |
| `UnshieldedUtxo` | type |
| `ZswapLedgerEvent` | type |
| `BlockHashQueryVariables` | type |
| `BlockHashQuery` | type |
| `ConnectMutationVariables` | type |
| `ConnectMutation` | type |
| `DisconnectMutationVariables` | type |
| `DisconnectMutation` | type |
| `FetchTermsAndConditionsQueryVariables` | type |
| `FetchTermsAndConditionsQuery` | type |
| `TransactionStatusQueryVariables` | type |
| `TransactionStatusQuery` | type |
| `DustLedgerEventsSubscriptionVariables` | type |
| `DustLedgerEventsSubscription` | type |
| `ShieldedTransactionsSubscriptionVariables` | type |
| `ShieldedTransactionsSubscription` | type |
| `UnshieldedTransactionsSubscriptionVariables` | type |
| `UnshieldedTransactionsSubscription` | type |
| `ZswapEventsSubscriptionVariables` | type |
| `ZswapEventsSubscription` | type |
| `BlockHashDocument` | const |
| `ConnectDocument` | const |
| `DisconnectDocument` | const |
| `FetchTermsAndConditionsDocument` | const |
| `TransactionStatusDocument` | const |
| `DustLedgerEventsDocument` | const |
| `ShieldedTransactionsDocument` | const |
| `UnshieldedTransactionsDocument` | const |
| `ZswapEventsDocument` | const |
| `Query` | namespace |
| `Subscription` | namespace |
| `QueryClient` | class |
| `HttpQueryClient` | namespace |
| `SubscriptionClient` | class |
| `WsSubscriptionClient` | namespace |
| `ConnectionHelper` | namespace |
| `QueryRunner` | namespace |

## Appendix B: Evidence Index

Complete index of all verification evidence, organized by test tier
and package.

### devnet

| Test | Tier | Status |
|------|------|--------|
| devnet/error | tier3 | error |

### standalone

| Test | Tier | Status |
|------|------|--------|
| abstractions/WalletSeed-from-bytes | tier1 | pass |
| abstractions/WalletSeed-from-string | tier1 | pass |
| abstractions/WalletSeed-is-predicate | tier1 | pass |
| abstractions/WalletState-from-string | tier1 | pass |
| abstractions/SerializedTransaction-of | tier1 | pass |
| abstractions/ProtocolVersion-construction | tier1 | pass |
| abstractions/ProtocolVersion-min-max | tier1 | pass |
| abstractions/ProtocolVersion-range-within | tier1 | pass |
| abstractions/ProtocolVersion-is-predicate | tier1 | pass |
| abstractions/NetworkId-well-known | tier1 | pass |
| abstractions/SyncProgress-create | tier1 | pass |
| abstractions/SyncProgress-strictly-complete | tier1 | pass |
| abstractions/SyncProgress-not-complete | tier1 | pass |
| address-format/BLSScalar-is-32-bytes | tier1 | pass |
| address-format/ScaleBigInt-encode-decode-roundtrip | tier1 | pass |
| address-format/ScaleBigInt-zero | tier1 | pass |
| address-format/ScaleBigInt-large-value | tier1 | pass |
| address-format/ShieldedCoinPublicKey-construction | tier1 | pass |
| address-format/ShieldedCoinPublicKey-equality | tier1 | pass |
| address-format/ShieldedEncryptionPublicKey-construction | tier1 | pass |
| address-format/ShieldedAddress-composition | tier1 | pass |
| address-format/UnshieldedAddress-hex-roundtrip | tier1 | pass |
| address-format/UnshieldedAddress-equality | tier1 | pass |
| address-format/DustAddress-from-bigint | tier1 | pass |
| address-format/DustAddress-rejects-over-modulus | tier1 | pass |
| address-format/DustAddress-validates-modulus | tier1 | pass |
| address-format/MidnightBech32m-encode-shielded | tier1 | pass |
| address-format/MidnightBech32m-encode-unshielded | tier1 | pass |
| address-format/MidnightBech32m-roundtrip | tier1 | pass |
| capabilities/Imbalances-empty | tier1 | pass |
| capabilities/Imbalances-fromEntry | tier1 | pass |
| capabilities/Imbalances-merge | tier1 | pass |
| capabilities/Imbalances-fromEntries | tier1 | pass |
| capabilities/Imbalances-typeSet | tier1 | pass |
| capabilities/InsufficientFundsError-construction | tier1 | pass |
| capabilities/chooseCoin-selects-smallest | tier1 | pass |
| capabilities/chooseCoin-returns-undefined-for-wrong-type | tier1 | pass |
| capabilities/getBalanceRecipe-basic | tier1 | pass |
| capabilities/SubmissionError-construction | tier1 | pass |
| capabilities/SubmissionError-with-cause | tier1 | pass |
| capabilities/ProvingError-construction | tier1 | pass |
| capabilities/ProvingError-tag | tier1 | pass |
| capabilities/PendingTransactions-namespace-exists | tier1 | pass |
| hd/mnemonic-generates-24-words | tier1 | pass |
| hd/mnemonic-validates-roundtrip | tier1 | pass |
| hd/invalid-mnemonic-rejected | tier1 | pass |
| hd/random-seed-length | tier1 | pass |
| hd/wallet-from-seed | tier1 | pass |
| hd/roles-values | tier1 | pass |
| hd/derive-key-at-index-0 | tier1 | pass |
| hd/different-roles-different-keys | tier1 | pass |
| hd/different-accounts-different-keys | tier1 | pass |
| hd/composite-role-keys | tier1 | pass |
| hd/deterministic-derivation | tier1 | pass |
| indexer-client/Connect-query-exists | tier1 | pass |
| indexer-client/Disconnect-query-exists | tier1 | pass |
| indexer-client/BlockHash-query-exists | tier1 | pass |
| indexer-client/TransactionStatus-query-exists | tier1 | pass |
| indexer-client/QueryClient-is-context-tag | tier1 | pass |
| indexer-client/SubscriptionClient-is-context-tag | tier1 | pass |
| indexer-client/ConnectionHelper-deriveWebSocketUrl | tier1 | pass |
| indexer-client/QueryRunner-runPromise | tier1 | pass |
| utilities/SafeBigInt-namespace-exists | tier1 | pass |
| utilities/DateOps-namespace-exists | tier1 | pass |
| utilities/ArrayOps-namespace-exists | tier1 | pass |
| utilities/BlobOps-namespace-exists | tier1 | pass |
| utilities/InvalidProtocolSchemeError-construction | tier1 | pass |
| utilities/FailedToDeriveWebSocketUrlError-construction | tier1 | pass |
| utilities/ClientError-construction | tier1 | pass |
| utilities/ServerError-construction | tier1 | pass |
| wallets/DustWallet-is-factory-function | tier1 | pass |
| wallets/ShieldedWallet-is-factory-function | tier1 | pass |
| wallets/UnshieldedWallet-is-factory-function | tier1 | pass |
| wallets/DustWalletState-has-mapState | tier1 | pass |
| wallets/ShieldedWalletState-has-mapState | tier1 | pass |
| wallets/UnshieldedWalletState-has-mapState | tier1 | pass |
| wallets/createKeystore-from-random-key | tier1 | pass |
| wallets/PublicKey-fromKeyStore | tier1 | pass |
| wallets/WalletBuilder-init | tier1 | pass |
| wallets/Runtime-namespace-exists | tier1 | pass |
| wallets/WalletRuntimeError-construction | tier1 | pass |
| wallets/FacadeState-exists | tier1 | pass |
| wallets/BalancingRecipe-isRecipe | tier1 | pass |
| wallets/BalancingRecipe-getTransactions | tier1 | pass |
| wallets/WalletFacade-init-is-static | tier1 | pass |

### typecheck

| Test | Tier | Status |
|------|------|--------|
| typecheck/abstractions | tier1 | pass |
| typecheck/address-format | tier1 | pass |
| typecheck/capabilities | tier1 | pass |
| typecheck/dust-wallet | tier1 | pass |
| typecheck/facade | tier1 | pass |
| typecheck/hd | tier1 | pass |
| typecheck/node-client | tier1 | pass |
| typecheck/prover-client | tier1 | pass |
| typecheck/runtime | tier1 | pass |
| typecheck/utilities | tier1 | pass |


## Appendix C: Version Information

| Component | Version |
|-----------|---------|
| Wallet SDK upstream | a1da64624b6a49aeed66d4f6466dee51fbab98ef |
| Node | 0.22.0-rc.6 |
| Indexer | 4.0.0-rc.5 |
| Proof Server | 8.0.2 |
| Ledger | v8 |
| TypeScript | 5.9.3 |
| Node.js | 22 |
