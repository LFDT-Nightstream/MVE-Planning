# C4 Level 4 — Code Documentation

## Zero-Knowledge Proving Application Service and Delegation Client

**Status:** Reference Implementation Guide
**Version:** 1.0
**Companion to:** Systems Design Document v1.0, STRIDE Threat Model v1.0, C4 Architecture Document v1.0
**Scope:** Procedural containers only (application service, delegation client)

---

## 1. Introduction and Scope

This document completes the C4 hierarchy for the procedural portions of the ZK proving architecture. It decomposes the application service and delegation client containers to the class level, documenting each class's responsibilities, invariants, collaborators, lifecycle, and the test cases that should cover its behaviour.

The scope is deliberately limited. The proving circuit is not documented at Code level because circuits are constraint systems rather than procedural call graphs, and forcing them into a class-diagram idiom misrepresents their semantics. The prover service's internals are determined by the chosen proving library and are covered by that library's own documentation. The Vault cluster's internals are HashiCorp's codebase and are out of scope. The Component diagram in the C4 architecture document remains the appropriate level of detail for those containers.

This document is written for the engineers implementing the system. It assumes familiarity with the Component diagram's decomposition and with the security properties documented in the systems design and threat model documents. Where a class's design is driven by a specific threat, the threat ID from the STRIDE model is cited.

The language of the document is deliberately implementation-agnostic. Classes and methods are described at a semantic level rather than as specific language constructs. Readers implementing in Rust, Go, Java, TypeScript, or any other language should adapt the idioms to their platform while preserving the invariants and contracts specified here.

---

## 2. Application Service — Class-Level Design

The application service decomposes into twelve classes across six components. Each component's classes are covered in turn, with the classes' interfaces, invariants, collaborators, and testing concerns.

### 2.1 Request Handler Component

#### 2.1.1 RequestController

The `RequestController` is the outermost class in the application service. It receives incoming requests over the service's public interface (HTTP, gRPC, or message queue depending on deployment), validates their structure, and drives the rest of the proving pipeline.

Its primary responsibilities are authenticating the calling end-user, extracting a structured `Intent` object from the raw request, performing application-specific business-logic authorisation (not cryptographic authorisation — that is the delegation system's job), and returning the final response to the caller.

The critical invariant is that business-logic authorisation happens *before* any cryptographic work. A request that fails business-logic authorisation must be rejected before a session key is generated, before a delegation is requested from Vault, and before any prover resources are reserved. Violating this invariant turns every unauthorised request into a potential DoS vector against Vault and the prover, because each such request consumes resources even though it will ultimately be rejected.

The `RequestController` collaborates with the `ScopeDeriver` to translate the validated `Intent` into a concrete `Scope` specification. It does not interact with the `DelegationClient` or the `WitnessBuilder` directly; those interactions are coordinated by a higher-level `ProvingPipeline` orchestration that is not shown as a separate class in the diagram but should exist in the implementation to keep `RequestController` focused on request handling.

Test coverage for `RequestController` should include valid-request happy paths, malformed request rejection, authentication failure, business-logic authorisation failure, rate-limit rejection, and the interaction with upstream errors (what does the caller see when Vault is unavailable, when the prover times out, when the verifier rejects the proof).

#### 2.1.2 ScopeDeriver

The `ScopeDeriver` converts an `Intent` — a representation of what the caller wants to do — into a `Scope` — a structured specification of the authority required to do it. The conversion is rule-driven and should be implemented as a pure function of the intent plus a static configuration.

The critical invariant is that the derived scope is the *minimum* scope that authorises the intent. Over-broad scopes silently expand the blast radius of any compromise. A caller who wants to transfer 100 units should receive a scope with `amountCap = 100`, not a scope with `amountCap = unlimited` that happens to accommodate this transfer. Tests should include boundary cases that verify the scope is actually minimal for each intent class.

The `ScopeDeriver` consults an allowlist configuration that defines which intent classes may derive which scope dimensions. This configuration is the operational security boundary between business logic and cryptographic authorisation; changes to it should be gated behind security review. The configuration format should be expressible in the same canonical encoding used for the on-wire scope representation, to avoid translation bugs between human-readable policy and machine-enforced scope.

Threats addressed: T4 (scope grammar tampering) — the `ScopeDeriver`'s allowlist is the application-side half of the agreement that the circuit enforces on the other side. The threat model's highest-risk item depends on this class producing scopes that exactly match what the circuit will accept.

### 2.2 Session Key Manager Component

#### 2.2.1 SessionKeyFactory

The `SessionKeyFactory` generates ephemeral keypairs for proving sessions. Each call to `generate()` produces a fresh `SessionKeypair`; no keypair is ever reused across sessions.

The critical invariant is entropy quality. The factory must seed from the operating system's cryptographically secure random source (`/dev/urandom`, `getrandom(2)`, `CryptGenRandom`, or the platform equivalent). Userspace PRNGs, even cryptographically strong ones, are unacceptable unless they are seeded from the OS source at the start of each keypair generation. The factory should not cache or reuse random material across calls, because doing so creates a window where a memory compromise recovers future keys.

The factory is stateless beyond the entropy source; making it a singleton or a per-request instance is an implementation choice without security implications. Tests should include statistical entropy checks on generated keypairs, verification that repeated calls produce distinct keypairs, and verification that the generated public key correctly corresponds to the private key under the chosen signature scheme.

Threats addressed: I3 (session key disclosure) — the factory is the sole source of session keys, so any weakness here propagates to every proof the system generates.

#### 2.2.2 SessionKeypair

The `SessionKeypair` holds a keypair for a single proving session. Its interface exposes the private key and public key for use by downstream components, and a `zeroise()` method that overwrites the private key material in memory.

The critical invariants are non-copyability and finalisation. The keypair must not be copyable — duplicating it creates additional memory locations that `zeroise()` cannot reach. In languages with ownership (Rust), this is enforced by the type system. In languages with move semantics (C++), it is enforced by deleting the copy constructor. In garbage-collected languages (Java, Go), it requires discipline: passing references rather than copying, using `try-with-resources` or `defer` to ensure `zeroise()` is called, and accepting that the GC may retain copies in write barriers beyond the programmer's control. This GC limitation is one reason this class is particularly awkward in GC'd languages; it is not a reason to abandon the invariant, but it is a reason to augment it with process-level protections (smaller heap, frequent full GC, mlock to prevent swap-out).

The keypair must zeroise on drop in all cases — normal completion, exceptional exit, and timeout. This typically requires a finaliser, a destructor, or a `try-finally` wrapper at every call site. Missing the zeroisation on an exceptional path is a recurring source of post-mortem-discovered key exposure in systems of this shape.

Tests should verify that zeroisation actually overwrites memory (memory inspection tests with platform-specific tooling), that the keypair cannot be copied (compile-time tests where the language permits), and that zeroisation happens on all exit paths (coverage analysis of exception handlers).

Threats addressed: I3, I4 (side-channel witness recovery) — a properly-zeroised keypair limits both direct memory disclosure and side-channel attacks that require the key to be in memory at a specific moment.

### 2.3 Delegation Client Component

This component is complex enough to warrant its own dedicated section. The delegation client's internal class structure is documented in section 3. The facade `DelegationClient` class and the `Delegation` value type are summarised here for completeness of the application-service view.

#### 2.3.1 DelegationClient (facade)

The `DelegationClient` presents a simple interface (`request(scope, pk) → Delegation`) to the rest of the application service, hiding the complexity of Vault authentication, message preparation, retry policy, and caching. Its detailed decomposition appears in section 3.

The critical invariant at this level is that a successful `request` returns a delegation that is cryptographically valid, unexpired, and matches the requested scope and session public key. If any of those properties cannot be guaranteed, the call must fail rather than return a suspect delegation. The application service's downstream components trust the `Delegation` they receive; weakening this invariant undermines the entire pipeline.

#### 2.3.2 Delegation

The `Delegation` value type represents a signed delegation as received from Vault. Its fields are the signature, the signed message, the scope, and the expiry. It exposes `isExpired(now)` for runtime expiry checking and `canonicalBytes()` for producing the canonical encoding used in the witness.

The critical invariant is immutability. Once constructed, a `Delegation` must not be modifiable; any field change would invalidate the signature without the signature itself changing, and any code that accepts the delegation as valid would now accept a forgery. In Rust this is the default; in Go and Java it requires deliberate design (unexported fields, defensive copying of slices); in TypeScript it requires `readonly` markers and runtime freezing for defence-in-depth.

Tests should verify that the delegation rejects tampering attempts (modifying scope or expiry after construction should either be impossible or detectable), that `isExpired` handles clock-skew edge cases, and that `canonicalBytes()` produces byte-identical output to the canonical encoding library.

### 2.4 Witness Builder Component

#### 2.4.1 WitnessBuilder

The `WitnessBuilder` assembles the proving circuit's witness from the delegation, the session keypair, and the intent's public inputs. It partitions the data into private (witness) and public (public inputs) sections and produces a structured object ready to hand to the prover.

The critical invariant is the correct private/public partition. Private data — the delegation signature, the session private key, the scope if the application requires scope privacy — must never appear in the public inputs. Public data — `pk_root`, the current time, the statement — must always appear in the public inputs, because anything not in the public inputs is invisible to the verifier. Getting this partition wrong causes either information disclosure (private data leaked to the verifier as public inputs) or verification failure (public data hidden as private, so the verifier cannot check it).

The `WitnessBuilder` is the application-service consumer of the shared `CanonicalEncoding` library. Every byte that enters the witness via this class must be encoded through the shared library, not via ad-hoc serialisation. This is the single most important discipline in the component; a tempting shortcut here — "just pack the scope into a byte array, it's obvious" — is precisely the kind of code that causes scope-grammar disagreement (T4).

The witness itself is a sensitive object. It contains the delegation signature and the session private key. The `WitnessBuilder` should produce a witness with the same non-copyable, zero-on-drop semantics as the `SessionKeypair`, and the witness's lifetime should be bounded to the proving call.

Tests should include round-trip tests against the canonical encoding library (witness-encoded data decodes to the same values), partition tests (every input ends up on the correct side of the private/public boundary), and adversarial tests (can a malformed intent cause the builder to misclassify a field).

Threats addressed: T4 (scope grammar tampering), I3 (session key disclosure via witness mismanagement).

#### 2.4.2 CanonicalEncoding (shared library)

The `CanonicalEncoding` is not strictly a class of the application service; it is a shared library used by the application service, the circuit, and the Vault-side message preparation. It is documented here because the application service is one of its consumers and because its correctness is load-bearing for the entire architecture.

The critical invariant is byte-identical output across all three consumers. If Vault's message preparer produces bytes A, the application's witness builder must produce bytes A, and the circuit's in-circuit decoder must produce the same field elements that A maps to. Any divergence is a security bug. This invariant cannot be maintained if each consumer implements the encoding independently; the only reliable approach is a single shared implementation, compiled or transpiled into each consumer as needed, with exhaustive cross-consumer tests.

The encoding itself should use fixed-width fields and length-prefixed concatenation, not JSON, not Protobuf without determinism flags, not any format whose canonical form is underspecified. Hashing to field elements should use a ZK-friendly hash (Poseidon, Rescue) to minimise circuit cost. The encoding's specification should be versioned, and any change to the specification should bump the version; mixing versions is a scope-grammar disagreement vulnerability.

Tests are unusual for this class because they span three parties. The test suite should include conformance vectors — fixed input/output pairs — that every implementation must pass, fuzz-generated inputs encoded by each implementation and compared byte-for-byte, and boundary cases around all field widths and type conversions.

Threats addressed: T4 — this class is the primary defence against the highest-risk threat in the model.

### 2.5 Prover Orchestrator Component

#### 2.5.1 ProverOrchestrator

The `ProverOrchestrator` passes a witness to the prover service, receives the resulting proof, and handles timeouts and retries. It is the application-service end of the inter-container channel to the prover.

The critical invariant is witness zeroisation on all exit paths. Proving is slow (seconds to minutes); during that time the witness lives in two places — the orchestrator's memory and the prover's memory. The orchestrator's copy must be zeroised the moment it has been transmitted to the prover, and its local reference must be dropped. Keeping the witness in orchestrator memory for the duration of the proving call doubles the exposure window for no reason.

Timeouts are a correctness concern, not just a liveness concern. A proving call that runs indefinitely holds a witness in memory indefinitely, and more importantly, ties up a prover slot that legitimate callers need. The orchestrator must enforce a hard timeout with a value tuned to the circuit's expected proving time plus a reasonable margin; callers whose proofs exceed this bound must receive an explicit failure, not a longer-than-expected success.

Retries on prover failure are permitted but must be bounded. A prover failure could be transient (OOM, network blip to the prover service) or persistent (circuit bug, malformed witness). The orchestrator should retry a small fixed number of times with short backoff, then fail to the caller. Unbounded retry is a DoS vector.

Tests should include successful proving paths, timeout enforcement, retry behaviour on transient failures, abandonment on persistent failures, and verification that witness zeroisation happens at the expected points in all paths.

Threats addressed: D3 (prover resource exhaustion), I3 (session key disclosure via witness retention).

#### 2.5.2 ProverTransport

The `ProverTransport` handles the inter-container communication to the prover service. Its implementation depends on the deployment: in a single-pod deployment it is typically a Unix domain socket with peer credential verification; in a multi-pod deployment it is mTLS with strict certificate validation.

The critical invariant is authentication of both endpoints. The prover must only accept witnesses from the legitimate application service, and the application service must only send witnesses to the legitimate prover. A compromised sidecar container in the same pod is the threat that peer-credential Unix sockets protect against; a compromised pod in the same cluster is the threat that mTLS protects against. Choosing the right transport for the deployment's threat model is an architectural decision that should be made explicitly and documented in the deployment manifest.

The transport should not log witness contents. Standard HTTP libraries often log request bodies at debug level; this must be disabled, or the transport should not use such a library. A debug-log with witness contents is indistinguishable from a secret-leak vulnerability.

Tests should include authenticated happy paths, rejection of unauthenticated peers, rejection of malformed messages, and verification that no witness data appears in any log output at any log level.

### 2.6 Submission Client Component

#### 2.6.1 SubmissionClient

The `SubmissionClient` delivers the proof and public inputs to the verifier. Its implementation varies by verifier type: for on-chain verifiers it constructs and submits a transaction; for off-chain verifiers it makes an authenticated HTTP call or similar.

The critical invariant is that the submission carries only public data. The proof is public by construction, the public inputs are public by definition, and any additional submission metadata (caller identity, timestamps, request IDs) should be treated carefully — it may compromise the unlinkability properties that the proof system provides. If the application's threat model does not require unlinkability, this is not a concern; if it does, the submission client is where that requirement is either upheld or violated.

The submission client is also where any non-repudiation layer would live. If the application requires that a proof be cryptographically linked to a specific caller (see R2 in the threat model — the core architecture does not provide this), the submission client wraps the proof in a signed submission envelope. This wrapping is an application-layer concern, not a proving-system concern, and is documented here for completeness rather than because it is architecturally required.

Tests should include submission happy paths, verifier-error handling (distinguishing "proof rejected" from "verifier unreachable"), and for applications with unlinkability requirements, verification that no caller-identifying data appears in the submission.

---

## 3. Delegation Client — Class-Level Design

The delegation client is pulled out for dedicated treatment because its six classes implement the most security-critical sequence in the application service, and because their interactions are subtle enough that shallow documentation is misleading.

### 3.1 DelegationClient (facade)

The facade coordinates the other five classes to satisfy a delegation request. A typical call sequence is: check the cache; if a valid cached delegation exists for the (scope, pk) pair, return it. Otherwise, fetch a Vault token, prepare the canonical message, wrap the signing call in the retry policy, invoke the transport to obtain the signature, construct the `Delegation` value object, store it in the cache, and return it.

The critical invariant is correctness of the cache-first path. If the cache returns a stale delegation (past its expiry, or for a different pk), the facade must detect this and proceed to the Vault fetch path; returning a stale delegation to the caller is a correctness bug that would cause proof verification to fail at the verifier. The facade's tests must include cache-hit-stale scenarios, not just cache-hit-fresh and cache-miss.

The facade is the only class in the delegation client with a public interface; the other five are internal. This encapsulation is deliberate: callers should not interact with the cache, token manager, or transport directly, because the correctness of the delegation flow depends on these components being invoked in the right order and only through the facade's coordination.

### 3.2 DelegationCache

The cache stores delegations obtained from Vault and serves subsequent requests for the same (scope, pk) pair without re-hitting Vault, subject to the delegation's expiry.

The critical invariants are three. First, the cache is in-memory and per-session — never persisted to disk, never shared across application processes. Persistence would save Vault calls at the cost of making delegation theft dramatically easier; any attacker who compromises the host and dumps its filesystem recovers every cached delegation, bypassing the short-TTL defence. Second, the cache key must include `pk_session`, not just the scope. A cached delegation for (scope_A, pk_1) must not be returned for a request for (scope_A, pk_2), because the delegation binds to `pk_session` and using it with a different session key would fail circuit verification. Third, expiry must be enforced on lookup, not only on eviction; a delegation that expires between its store and its next lookup must not be returned.

The cache size should be bounded. An unbounded cache in a long-running service accumulates expired delegations and wastes memory; a bounded cache with LRU eviction handles this. The bound should be generous relative to expected concurrent sessions — eviction should be a rare event driven by session churn, not a frequent event that forces re-fetches.

Tests should include cache-hit/miss paths, expiry-at-lookup, the (scope, pk) collision case, and eviction under bounded size.

Threats addressed: I2 (delegation credential disclosure) — the cache's in-memory-only invariant is the primary operational defence against disk-based delegation theft.

### 3.3 VaultTokenManager

The token manager obtains and maintains the Vault token used to authenticate delegation requests. It exchanges the application's workload-bound token (Kubernetes ServiceAccount JWT, cloud IMDS credential, AppRole secret-id) for a Vault token with a short TTL, and refreshes it before expiry.

The critical invariant is that the token manager holds exactly one Vault token at a time, and refreshes it before expiry rather than after. Letting the token expire and refreshing on the next use introduces a latency spike into the delegation path that can cascade into proof generation timeouts. The refresh should happen proactively, typically at 70–80% of the token's TTL.

The workload-bound token should be treated as equivalent to a Vault token for purposes of secrecy and audit; its compromise grants the same capabilities. The token manager should not log either token at any level, and should not expose them through any interface other than the internal `getToken()` method.

Tests should include initial token acquisition, proactive refresh, handling of refresh failure (the manager should fail the current request but retry refresh on the next request rather than entering a permanent failure state), and verification that tokens do not appear in logs.

### 3.4 MessagePreparer

The `MessagePreparer` constructs the canonical delegation message from the scope, session public key, and expiry. It is a thin wrapper over the shared `CanonicalEncoding` library, with application-specific responsibility for injecting the current timestamp (from the configured time source), generating a fresh nonce, and applying the configured expiry offset.

The critical invariant is that the preparer must not add any encoding logic beyond what the shared library provides. If the application has a field that the shared library does not know how to encode, the library must be extended; the preparer must not implement ad-hoc encoding for that field. Violating this invariant is the most likely path to a scope-grammar disagreement (T4), because the library's other consumers (circuit, Vault-side) will not know about the preparer's custom encoding.

Nonce generation should use the same entropy source as the session key factory; a weak nonce is a signature forgery vector for some signature schemes. The nonce should be wide enough that birthday collisions are negligible over the system's expected lifetime of delegations.

Tests should include conformance against the shared library's test vectors, nonce uniqueness under load, correct timestamp injection, and boundary cases around TTL configuration.

Threats addressed: T4 (scope grammar tampering) via strict delegation to the shared library.

### 3.5 RetryPolicy

The `RetryPolicy` wraps the Vault signing call and handles transient failures with exponential backoff and jitter.

The critical invariant is that the policy must distinguish transient failures (retryable) from permanent failures (not retryable). HTTP 429 (rate-limited) with a `Retry-After` header is retryable; HTTP 403 (policy denied) is not. Network-level failures (connection refused, TLS error) are retryable with backoff; malformed-request failures (HTTP 400 with a body indicating a client bug) are not. Retrying on permanent failures compounds the failure — it wastes Vault capacity and delays the inevitable error return to the caller.

The policy must also handle the subtle case of lost-response semantics. If the policy's HTTP call fails after Vault has successfully signed and audited a delegation but before the response reaches the caller, a retry would cause Vault to sign and audit a second delegation. This is not a security bug — both delegations are valid — but it is an operational nuisance that inflates audit logs and Vault load. Idempotency keys in the delegation request, if Vault's configuration supports them, eliminate this; otherwise, the policy should limit retries on post-send failures to avoid duplication.

Backoff should use full jitter (a random delay between zero and the nominal backoff), not fixed backoff or equal jitter. Full jitter prevents synchronised retry storms from coordinated callers.

Tests should include retryable-error paths with correct backoff timing, non-retryable-error paths with immediate failure, the lost-response scenario, and jitter distribution verification.

Threats addressed: D1 (Vault delegation flooding) — correct retry behaviour prevents retry-amplification under load.

### 3.6 VaultTransport

The transport handles the actual HTTP call to Vault's sign endpoint, including TLS setup, certificate validation, and request/response serialisation.

The critical invariants are two. First, certificate pinning or a narrow trust store is mandatory. A permissive trust store (the system CA bundle) means any CA compromise — and there are hundreds of CAs in the default trust store, some of which have been compromised historically — becomes a delegation-forging vulnerability. The transport should trust only the specific CA that issued Vault's certificate, or pin the certificate itself. Second, the transport is the only outbound network-accessing class in the delegation client; all Vault traffic must flow through it. This enables a simple network policy (egress to Vault is permitted only from this class's network identity) and a simple code review (searching for Vault network access is searching for uses of this class).

The transport should use HTTP/2 for connection reuse and header compression, reducing latency under load. Connection pooling reduces the per-call TLS handshake cost but should be configured with a bounded pool size to prevent resource leaks on Vault-side connection churn.

Tests should include successful signing paths, TLS validation failure, certificate pin mismatch, connection reuse behaviour, and pool exhaustion handling.

Threats addressed: S2 (Vault impersonation) — the cert pinning here is the defence in depth behind the in-circuit signature verification.

---

## 4. Cross-Cutting Implementation Concerns

Several concerns cut across the classes documented above and warrant consolidated treatment.

### 4.1 Secret Lifecycle Discipline

The classes that handle secret material — `SessionKeypair`, `Witness`, Vault tokens, delegation signatures — share a common discipline: non-copyability, bounded lifetime, zeroisation on drop. Implementing this discipline consistently across the codebase is more important than any individual class's correctness, because a secret that escapes the discipline (copied into a log, retained in a cache, held across an async yield) loses the properties the discipline was supposed to guarantee.

The recommended implementation pattern is a single `Secret<T>` wrapper type used everywhere secret material lives, with non-copyability enforced by the type system, a destructor that zeroises, and careful integration with the language's async machinery if relevant. Passing `Secret<T>` by move or reference ensures there is at most one live copy at any time; the compiler enforces this where the language permits.

Logging frameworks should be configured to refuse to format `Secret<T>` values, replacing them with a placeholder. This prevents the most common accidental leak — a log line that includes a struct containing a secret field.

### 4.2 Error Handling and Information Disclosure

Errors returned to callers should not leak information about internal state. A delegation request that fails because of a Vault policy denial should return a generic "authorisation failed" to the caller, not "Vault policy engine rejected scope dimension 'amountCap' for caller with identity X" — the detailed error should be logged, not returned. This is a standard web-application concern but is particularly important here because the detailed errors reveal the structure of the scope grammar, which is useful information to an attacker probing for scope-grammar disagreement bugs.

Internally, errors should carry enough context for operators to diagnose failures. The tension between caller-facing information hiding and operator-facing diagnostic richness is resolved by logging the full context under a correlation ID and returning only the correlation ID to the caller.

### 4.3 Observability Without Secret Leakage

Every class documented above should produce metrics and traces. Every class documented above must not include secret material in those metrics and traces.

Metrics should focus on counts, latencies, and error rates. "Delegations issued per scope" is fine; "delegations issued with signature starting 0x..." is a leak. Traces should include request IDs, component identities, and timing, but not field values that are or contain secret material.

The `CanonicalEncoding` library should provide a "fingerprint" function that hashes a canonical message to a short identifier suitable for logging. This lets operators correlate a delegation across components without any component ever logging the delegation itself. The fingerprint should be deterministic (same message → same fingerprint) but not reversible (fingerprint → message is computationally infeasible).

### 4.4 Concurrency and Async Boundaries

Several classes above have lifetime invariants that interact subtly with async execution. A `Witness` that is dropped across an await point must zeroise before the await; otherwise the witness sits in memory for the entire suspension duration. Language-specific care is required: Rust's async drops are well-defined; Go's goroutines and deferred calls require deliberate structuring; Java's virtual threads have their own gotchas.

The safest pattern is to keep secret-handling code in synchronous sections, with async boundaries only between such sections. A proving pipeline that synchronously fetches a delegation, synchronously builds a witness, synchronously submits it to the prover, then awaits the proof result, then synchronously zeroises, is easier to reason about than one that threads async through the secret-handling logic.

### 4.5 Dependency Discipline

The delegation client in particular has a small, well-defined set of external dependencies: an HTTP client library, a cryptographic library for hashing, the shared canonical encoding library, and a logging framework. Each dependency is a potential supply-chain attack surface (see the out-of-scope note in the threat model's section 11). The dependency list should be minimal, pinned to specific versions, verified against published checksums, and audited for vulnerability disclosures on a scheduled cadence.

Adding a new dependency to the delegation client should be treated as a security-significant change. Adding a new dependency to the shared `CanonicalEncoding` library should be treated as a critical change, because that library runs inside Vault's message preparation path, inside the application service, and inside the circuit's encoder — a compromised dependency there compromises all three.

---

## 5. Testing Strategy

Code-level testing for this architecture has four layers.

Unit tests cover individual classes in isolation. Each class's test suite should include the happy path, boundary cases, error paths, and adversarial cases (malformed inputs, injected failures). The tests referenced inline in sections 2 and 3 constitute the minimum; production implementations should exceed this.

Integration tests cover the interactions between classes. The most important integration tests exercise the full delegation flow (`RequestController` → `DelegationClient` → Vault → `WitnessBuilder` → `ProverOrchestrator` → mock prover) against a real Vault instance in a test configuration. These tests catch integration bugs that unit tests miss, particularly around the canonical encoding and the retry policy.

Cross-consumer tests specifically target the `CanonicalEncoding` library. The test suite should include conformance vectors that every consumer passes, differential tests that feed the same input through each consumer and compare outputs, and fuzz tests that generate random valid inputs and verify round-trip correctness. These tests are the primary defence against T4 (scope grammar tampering) and should be treated as critical.

End-to-end tests cover the full proving pipeline against a real verifier. These tests are slow (seconds to minutes per proof) and should run less frequently than unit and integration tests, but they are the only tests that exercise the real proving library and verify that the application's witness is actually valid for the deployed circuit.

Beyond functional tests, the codebase should be subject to static analysis for secret-handling patterns (no `Secret<T>` in logs, no secret fields in serialisable structs), memory sanitisation (valgrind, ASAN) for zeroisation correctness, and adversarial review focused on the scope-grammar agreement and canonical-encoding invariants.

---

## 6. Relationship to Higher-Level Documents

This document completes the C4 hierarchy but does not supersede the higher-level documents. The systems design document's architectural rationale, the STRIDE threat model's threat enumeration, and the C4 architecture document's Context/Container/Component views remain authoritative for their respective concerns.

When the higher-level documents and this Code-level document disagree, the higher-level documents win. This document's class designs are informed by architectural requirements; if a class design here suggests a different trust boundary, a different data flow, or a different security property than the higher-level documents specify, the class design is wrong. This rule exists because implementation drift is a common failure mode: the implementation evolves to solve problems the implementers encounter, the documentation does not, and over time the implementation's de facto architecture diverges from the documented one. Anchoring Code-level design to higher-level documents — and updating the higher-level documents when the architecture genuinely changes — is the discipline that keeps the documentation set coherent.

Changes to this document should be reviewed alongside changes to the Component diagram in the C4 architecture document. A new class is a Component-level event if it changes the component's responsibilities; a new class that is purely an internal refactoring of an existing component is a Code-level event only. The distinction is judgement-driven; when in doubt, review at both levels.

---

## 7. What is Not Documented Here

The proving circuit is not covered. Circuits are constraint systems and should be documented using constraint-system idioms — R1CS listings, custom gate specifications, lookup table schemas, constraint-count budgets — not UML-style class diagrams. Teams implementing the circuit should produce a dedicated circuit specification document using those idioms.

The prover service's internals are not covered. Those internals are determined by the chosen proving library (arkworks, halo2, circom, snarkjs, or a similar framework) and are documented by that library. The architectural contract the prover service must satisfy — accept witness, produce proof, zeroise — is documented in the Component diagram and the security invariants above, but the specific implementation is library-specific.

The Vault cluster's internals are not covered. Vault is a complex system in its own right, documented by HashiCorp. The architectural contract — authenticate callers, evaluate policy, sign canonical messages, audit outcomes — is documented in the Component diagram; Vault's implementation of that contract is Vault's concern.

Deployment topology, operational runbooks, monitoring dashboards, and incident response procedures are out of scope. These are operational documents that should be produced by the team operating the system, informed by but distinct from the architectural documentation.

---

## 8. Summary

The twelve classes of the application service and the six classes of the delegation client are the implementation surface of the signature-delegation ZK proving architecture. Their individual designs are straightforward; their collective discipline — consistent secret handling, rigorous canonical encoding, careful error handling, proper observability without leakage — is what gives the architecture its security properties.

No individual class is especially complex. The architecture's difficulty is in the invariants that span classes: that a session key is generated once and zeroised reliably, that a witness is built via the shared encoding and never persists beyond the proving call, that a delegation is cached in memory only and keyed by both scope and session, that Vault authentication and message preparation use a shared library rather than ad-hoc logic, that errors do not leak scope-grammar detail to callers, that observability does not leak secrets to logs. Implementing any one class while violating one of these invariants undoes the security of the whole. Implementing every class correctly is the work.
