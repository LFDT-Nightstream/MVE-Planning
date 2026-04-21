# STRIDE Threat Model

## Zero-Knowledge Proving with Vault-Mediated Signature Delegation

**Status:** Reference Threat Model
**Version:** 1.0
**Companion to:** Systems Design Document v1.0
**Methodology:** STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege)

---

## 1. Scope and Methodology

This document enumerates threats against the signature-delegation ZK proving architecture using the STRIDE taxonomy. It is intended as a companion to the systems design document and assumes familiarity with the component model described there.

STRIDE is applied per trust boundary crossing and per component. For each identified threat, this document records the asset at risk, the attacker capability required, the impact if the threat is realized, the existing architectural mitigations, and residual risk that operations or implementation must address. Threats are rated on a three-level scale (Low, Medium, High) reflecting the combined likelihood and impact after architectural mitigations are applied, not the raw untreated risk.

The threat model assumes the adversary capabilities documented in the systems design document: full compromise of the application container and its host, full network observation, and compromise of any number of past or concurrent prover instances. Vault itself, its unseal procedure, and its storage backend are out of scope — they are the root trust assumption and are governed by a separate Vault operational threat model.

---

## 2. System Decomposition and Trust Boundaries

The system comprises five principal components and four trust boundaries. The components are Vault (with its Transit mount, policy engine, and audit device), the application container (which hosts the prover), the delegation credential (a data asset in transit and at rest in application memory), the proving circuit (code and witness), and the verifier. The boundaries are the Vault process boundary, the application-to-Vault network boundary, the application-to-verifier network boundary, and the implicit boundary between the circuit's trusted semantics and the untrusted runtime that executes it.

Each STRIDE category is evaluated against these components and boundaries in the sections that follow.

---

## 3. Spoofing

Spoofing threats concern an attacker impersonating a legitimate principal to obtain unauthorized capabilities.

### 3.1 S1 — Attacker impersonates a legitimate application to Vault

An attacker with network access but without valid application credentials attempts to request delegations from Vault by impersonating a legitimate caller. If successful, they obtain scoped delegations and can generate proofs within those scopes.

The architectural mitigation is Vault's authentication layer. The application authenticates using a configured auth method (AppRole with periodic role-id/secret-id rotation, Kubernetes ServiceAccount tokens validated against the cluster's API server, JWT with short-lived signed tokens from a trusted IdP). Vault rejects delegation requests that do not carry valid authentication.

Residual risk is Medium. The architecture does not protect against theft of valid application credentials from a compromised application host — once credentials are stolen, the attacker's requests are indistinguishable from legitimate ones. This is addressed operationally through short credential TTLs, binding credentials to workload identity where possible (e.g. SPIFFE/SPIRE, cloud IMDS), and monitoring for anomalous issuance patterns.

### 3.2 S2 — Attacker impersonates Vault to the application

A network attacker intercepts the application's delegation request and returns a forged response, potentially containing a signature the attacker controls or a credential with attacker-chosen scope.

The architectural mitigation is TLS with certificate pinning or a restricted trust store, combined with the fact that any returned signature must verify under the known `pk_root` inside the circuit. A forged signature from a key the attacker holds will not verify against the legitimate `pk_root` and the proof will be unverifiable.

Residual risk is Low. The signature verification in the circuit is the strong mitigation; TLS is defense in depth. An attacker would need to both intercept the response and also compromise the pre-distributed `pk_root` in every verifier, which is a substantially harder attack than compromising a single channel.

### 3.3 S3 — Attacker impersonates the verifier

An attacker presents themselves to the prover as a legitimate verifier and receives proofs intended for the real verifier.

The architectural mitigation is that proofs are public by design — possession of a proof does not grant the holder any capability. A stolen proof attests a statement to whoever holds it, which is identical to its intended behavior. This is a non-threat in the core model; it only becomes a threat if the application's surrounding protocol treats proof delivery as authentication, which it should not.

Residual risk is Low, contingent on the application not misusing proofs as bearer credentials. Where proof submission must be authenticated, this is handled by the transport layer (mTLS, signed submission envelopes), not by the proof itself.

### 3.4 S4 — Attacker impersonates the caller identity embedded in a delegation

If the delegation includes an optional caller identity field, an attacker who compromises a lower-privileged caller attempts to request delegations claiming a higher-privileged identity.

The architectural mitigation is that Vault populates the caller identity field based on its own authentication context, not on a value supplied by the caller. The caller cannot claim an identity they did not authenticate as.

Residual risk is Low, provided the Vault policy correctly binds the signed identity to the authenticated entity and does not accept an attribute-supplied value.

---

## 4. Tampering

Tampering threats concern unauthorized modification of data or code.

### 4.1 T1 — Delegation message tampering in transit

An attacker intercepts the delegation in transit from Vault to the application and modifies the scope, expiry, or `pk_session` fields.

The architectural mitigation is that the delegation is a signature over the message. Any modification to the signed fields invalidates the signature, and the circuit will not produce a verifiable proof. TLS in transit provides defense in depth but is not the load-bearing control.

Residual risk is Low. The signature is cryptographically strong under the assumed hardness of EdDSA/BLS.

### 4.2 T2 — Witness tampering inside the application container

An attacker with write access to the application's memory modifies `sk_session` or the delegation to influence the resulting proof. In the worst case they substitute their own session keypair and a delegation they control.

The architectural mitigation is scope binding. A substituted delegation still must verify under `pk_root`, which means the attacker must already possess a valid Vault-issued delegation. If they do, they can generate proofs within that delegation's scope regardless of what they do to memory — the memory write is not adding capability, merely routing it through a different prover instance. If they do not possess a valid delegation, substitution fails at circuit verification.

Residual risk is Medium. This threat reduces to the general "compromised application container" case, which is explicitly in-scope for the architecture and bounded by scope and TTL.

### 4.3 T3 — Circuit tampering

An attacker modifies the proving circuit binary or its verification key, such that the circuit accepts inputs that should be rejected (for instance, accepting expired delegations or out-of-scope statements).

The architectural mitigation is that verification uses the *verifier's* copy of the verification key, not the prover's. A prover running a tampered circuit produces proofs that do not verify against the legitimate verification key. For this attack to succeed, the attacker must also compromise the verifier's verification key distribution.

Residual risk is Medium, concentrated in the verification key distribution channel. Verification keys should be distributed through signed release artifacts with reproducible builds, and high-value verifiers (e.g. on-chain contracts) should treat the verification key as immutable post-deployment or gate updates behind governance.

### 4.4 T4 — Scope grammar tampering

An attacker modifies the scope parser or scope allowlist in either Vault's policy layer or the circuit, causing the two sides to disagree on what a given scope means. If Vault's parser accepts a permissive interpretation that the circuit does not enforce (or vice versa), an attacker can obtain delegations that enable proofs beyond the policy-intended authority.

The architectural mitigation is shared canonical-encoding libraries used by both sides, combined with interoperability tests that enumerate scope interpretations at both sides and assert they agree.

Residual risk is High without disciplined implementation practice. Scope grammar disagreements are a classic source of authorization bypass in real systems. This threat should be the focus of implementation review and continuous testing.

### 4.5 T5 — Audit log tampering

An attacker modifies Vault's audit log to hide delegation issuances, frustrating forensic reconstruction after a compromise.

The architectural mitigation is Vault's support for tamper-evident audit devices (append-only storage, signed log entries, external SIEM forwarding). Operational practice should forward audit events to an independently controlled system in near-real-time so that local tampering is ineffective.

Residual risk is Low to Medium depending on audit device configuration. This is a Vault operational concern rather than a ZK-specific one.

---

## 5. Repudiation

Repudiation threats concern a party denying they performed an action, in the absence of evidence to the contrary.

### 5.1 R1 — Caller denies requesting a delegation

A compromised or malicious application caller generates proofs using a legitimately issued delegation, then denies having requested the delegation.

The architectural mitigation is Vault's audit log, which records every delegation issuance with the authenticated caller identity, the requested scope, the issued expiry, and a timestamp. Forensic review can unambiguously establish which caller requested which delegation.

Residual risk is Low, provided the audit log is reliably persisted and the Vault auth configuration cannot be bypassed.

### 5.2 R2 — Prover denies generating a specific proof

A specific proof is presented to the verifier; the prover claims not to have generated it.

The architecture deliberately provides no cryptographic link between a proof and the prover that generated it. This is a feature, not a bug — unlinkability is usually a privacy requirement. If the application's threat model requires non-repudiation of proof generation, this must be added at the transport layer through a signed submission envelope binding the proof to a caller identity.

Residual risk is application-dependent. The architecture does not address it, and applications that need non-repudiation must layer an independent mechanism.

### 5.3 R3 — Vault denies issuing a delegation

Vault is claimed to have issued a delegation that its operators dispute ever having been issued.

The architectural mitigation is the immutable audit log. In addition, the delegation signature itself is evidence under `pk_root`: anyone holding the delegation can prove to a third party that it was signed by the holder of `sk_root`.

Residual risk is Low.

### 5.4 R4 — Verifier denies verifying a proof

A verifier claims not to have accepted a proof that a counterparty believes was accepted.

Verification is deterministic given the proof, verification key, and public inputs. A third party can reproduce verification independently; there is nothing for the verifier to repudiate beyond their own downstream action on a successful verification. Downstream action repudiation is an application-layer concern (transaction logs, signed receipts) outside this threat model.

Residual risk is Low for the verification itself, application-dependent for downstream actions.

---

## 6. Information Disclosure

Information disclosure threats concern unauthorized exposure of confidential data. This is the category where the delegation architecture provides its most distinctive protections.

### 6.1 I1 — Root key disclosure

The root signing key `sk_root` is exposed outside Vault.

The architectural mitigation is that `sk_root` is never transmitted, never written to disk outside Vault's sealed storage, and never copied into any process memory other than Vault's. This is the central property the architecture is designed to preserve.

Residual risk is Low under the stated threat model (Vault-internal compromise is out of scope). Rolling `sk_root` is nonetheless a planned operational event and the rotation protocol must be tested.

### 6.2 I2 — Delegation credential disclosure

An attacker obtains a valid, unexpired delegation from a compromised application container.

The delegation is the primary credential that exists outside Vault, and its disclosure is explicitly in-scope for the architecture. The mitigation is not secrecy but *containment*: short TTL, narrow scope, and rate-limited issuance bound the blast radius.

Residual risk is Medium. This is the expected attack surface, and the architecture's security properties are defined in terms of it. Operations should monitor delegation reuse patterns — the same delegation being used to generate many proofs in rapid succession from unexpected network locations is a compromise indicator.

### 6.3 I3 — Session key disclosure

The ephemeral `sk_session` is disclosed alongside a delegation.

This has the same effective impact as delegation disclosure, because the two together constitute the proving capability. The same mitigations apply, plus the observation that `sk_session` is generated fresh per session and zeroized after use, so its disclosure window is already minimized.

Residual risk is Medium, equivalent to I2.

### 6.4 I4 — Witness disclosure via side channels during proving

An attacker with access to the proving host observes side channels (timing, cache behavior, memory access patterns, power consumption in exotic deployments) to recover witness bits.

The architectural mitigation is that the witness is not the root key — side-channel recovery of the witness yields the delegation and session key, which are already treated as compromisable. This is a significant advantage over architectures that place root material in the prover.

Residual risk is Low relative to architectures that prove knowledge of root material, Medium in absolute terms. Provers implemented in constant-time primitives further reduce this risk.

### 6.5 I5 — Scope disclosure to the verifier

The verifier learns scope information that the application intended to keep private. For example, in a financial application, the amount cap of the delegation might reveal information about the transacting party's authorization level.

The architectural mitigation is that scope fields can be declared as private inputs to the circuit rather than public inputs. The circuit constrains the statement's authorization context to match the private scope without revealing the scope itself. Which fields to hide is an application design decision driven by the privacy requirements.

Residual risk is application-dependent. The architecture permits both exposure modes; misuse is possible if an application treats a private scope as public or vice versa.

### 6.6 I6 — Caller identity linkage across proofs

An attacker correlates multiple proofs generated by the same caller by observing timing, network metadata, or application-layer artifacts.

The architecture deliberately does not link proofs cryptographically (see R2). Linkability via metadata is an application-layer and deployment concern. Mitigations include batching submission, mixnets or equivalent network anonymization, and ensuring that public inputs do not inadvertently carry caller-identifying data.

Residual risk is application-dependent and should be treated in a dedicated privacy analysis rather than this core threat model.

### 6.7 I7 — Disclosure via Vault audit log

The Vault audit log contains per-request caller identity, scope, and timestamp. An attacker with audit log access learns who requested what and when.

The architectural mitigation is that audit log access is a Vault policy matter, typically restricted to a small set of security operators with a strong need-to-know. The log does not contain `sk_root` or any session key material, so its disclosure is a privacy matter rather than an integrity compromise.

Residual risk is Low from an integrity perspective, Medium from a privacy perspective depending on the sensitivity of the caller-scope correlation.

---

## 7. Denial of Service

Denial of service threats concern degradation or elimination of legitimate availability.

### 7.1 D1 — Vault flooding via delegation requests

An attacker with valid credentials (or many attackers in aggregate) floods Vault with delegation requests, exhausting Vault's signing capacity or the attached audit device's write throughput.

The architectural mitigation is per-caller rate limiting in Vault policy, plus Vault's inherent rate-limit and quota support. Applications should also cache and reuse delegations within their TTL rather than requesting one per proof.

Residual risk is Medium. Legitimate bursty workloads can cause self-inflicted DoS if quotas are set tightly, and adversarial flooding from a compromised caller can impact other callers sharing the same Vault mount. Per-mount and per-namespace isolation helps.

### 7.2 D2 — Vault unavailability

Vault becomes unavailable due to unrelated cause (DR event, misconfiguration, upstream failure).

The architectural mitigation is that in-flight, unexpired delegations remain valid and can still be used to generate proofs. This is typically desirable — short Vault outages do not halt the proving pipeline. For longer outages, the TTL expires and new proofs become impossible until Vault recovers.

Residual risk is Medium for short outages, High for outages exceeding typical TTLs. Applications that require proving during extended Vault outages should consider longer TTLs with correspondingly narrower scopes.

### 7.3 D3 — Prover resource exhaustion

An attacker or heavy legitimate load exhausts the prover's CPU or memory, blocking proof generation.

The architectural mitigation is outside the scope of this model — this is standard capacity planning and resource isolation. Note that proving is computationally expensive (seconds to minutes per proof) and the system should not assume per-request proving latency matches per-request Vault latency.

Residual risk is Medium, addressed operationally via horizontal scaling of prover instances and work queue backpressure.

### 7.4 D4 — Verifier flooding with invalid proofs

An attacker submits many invalid proofs to the verifier, forcing verification work that will be rejected.

Verification is cheap (milliseconds) relative to proving, so this is typically a low-impact DoS. However, on-chain verifiers pay gas per verification, and high-volume invalid-proof submission could be costly.

Residual risk is Low for off-chain verifiers, Medium for on-chain verifiers where transaction-layer rate limiting or submission fees mitigate the attack economically.

### 7.5 D5 — Scope grammar evolution lockout

A circuit upgrade changes the scope grammar in a way that invalidates all existing delegations before their TTL expires, rendering the pipeline temporarily unable to produce verifiable proofs.

The architectural mitigation is parallel circuit operation during upgrade windows, with old circuits and verifiers running alongside new ones until old delegations expire naturally.

Residual risk is Low with disciplined change management, Medium without it.

---

## 8. Elevation of Privilege

Elevation of privilege threats concern an attacker gaining capabilities beyond what they were authorized to have.

### 8.1 E1 — Out-of-scope proof generation

An attacker with a legitimately issued delegation for scope A attempts to generate a proof for statement in scope B (where B is broader or different).

The architectural mitigation is the circuit's scope-constraint logic. The circuit verifies that the statement's authorization context equals the signed scope; a statement outside the signed scope cannot produce a satisfying witness regardless of the prover's intent.

Residual risk is Low provided the scope grammar is correctly specified and the circuit's scope-constraint logic is correctly implemented. This is the most security-critical section of circuit code and should receive disproportionate review attention. See T4 for the related scope-disagreement threat.

### 8.2 E2 — Expired delegation use

An attacker uses a delegation past its expiry to generate a proof.

The architectural mitigation is the circuit's expiry check against a public-input current time. A proof generated with an expired delegation against a current time past the expiry will not produce a satisfying witness.

Residual risk is Medium, concentrated in the trustworthiness of the public-input current time. If the verifier accepts a prover-supplied "current time" without external anchoring, expiry is effectively unenforced. On-chain verifiers using block timestamps are well-protected; off-chain verifiers must source time from a trusted authority (signed time service, TSA, TLS certificate validity proxy).

### 8.3 E3 — Vault policy bypass

An attacker exploits a misconfiguration or bug in Vault policy to request delegations outside their authorized scope.

The architectural mitigation is defense-in-depth Vault policy design: explicit scope allowlists per caller, policy review processes, least-privilege role definitions. Policy-as-code review before deployment is strongly recommended.

Residual risk is Medium, dominated by operational practice rather than cryptographic properties. Regular policy audits and drift detection reduce this risk.

### 8.4 E4 — Circuit soundness bug

The circuit contains a bug that permits generation of proofs for false statements.

The architectural mitigation is formal verification of critical circuits where feasible, rigorous peer review, extensive testing including adversarial fuzzing and known soundness-bug patterns (for instance, under-constrained field elements, missing range checks), and independent security audits for high-value deployments.

Residual risk is High without rigorous review, Low to Medium with professional audit. Circuit soundness bugs have caused real production incidents in the ZK ecosystem and should be treated as a primary concern.

### 8.5 E5 — Signature forgery in the delegation scheme

An attacker forges a signature under `pk_root` without access to `sk_root`, by exploiting a weakness in the chosen signature algorithm.

The architectural mitigation is using standardized, well-analyzed signature schemes (EdDSA on BabyJubJub, BLS on BLS12-381) with adequate security parameters. Exotic or bespoke schemes should not be used.

Residual risk is Low for well-studied schemes with appropriate parameters. Long-term cryptographic agility planning (for instance, post-quantum migration) should be considered for systems with multi-decade lifetimes.

### 8.6 E6 — Replay of a previously valid proof

An attacker replays a previously generated valid proof as if it were newly generated.

The architectural mitigation depends on the application. If the proof attests a statement tied to a nonce, block height, or unique transaction identifier in its public inputs, replay is cryptographically prevented. If the proof attests a timeless statement, replay is semantically valid — the statement was true and still is.

Residual risk is application-dependent. Applications should include anti-replay material in the public inputs when the statement's truth is time-bound.

---

## 9. Summary Risk Table

| ID | Category | Threat | Residual Risk |
|----|----------|--------|---------------|
| S1 | Spoofing | Caller impersonation to Vault | Medium |
| S2 | Spoofing | Vault impersonation to caller | Low |
| S3 | Spoofing | Verifier impersonation | Low |
| S4 | Spoofing | Caller-identity spoofing in delegation | Low |
| T1 | Tampering | Delegation in-transit modification | Low |
| T2 | Tampering | Witness modification in prover memory | Medium |
| T3 | Tampering | Circuit binary modification | Medium |
| T4 | Tampering | Scope grammar disagreement | High |
| T5 | Tampering | Audit log modification | Low-Medium |
| R1 | Repudiation | Caller denies delegation request | Low |
| R2 | Repudiation | Prover denies generating proof | App-dependent |
| R3 | Repudiation | Vault denies issuance | Low |
| R4 | Repudiation | Verifier denies verification | Low |
| I1 | Disclosure | Root key disclosure | Low |
| I2 | Disclosure | Delegation credential disclosure | Medium |
| I3 | Disclosure | Session key disclosure | Medium |
| I4 | Disclosure | Side-channel witness recovery | Low-Medium |
| I5 | Disclosure | Scope exposure to verifier | App-dependent |
| I6 | Disclosure | Cross-proof linkability | App-dependent |
| I7 | Disclosure | Audit log privacy | Low-Medium |
| D1 | DoS | Vault delegation flooding | Medium |
| D2 | DoS | Vault unavailability | Medium-High |
| D3 | DoS | Prover resource exhaustion | Medium |
| D4 | DoS | Verifier proof flooding | Low-Medium |
| D5 | DoS | Circuit upgrade lockout | Low-Medium |
| E1 | EoP | Out-of-scope proof generation | Low |
| E2 | EoP | Expired delegation use | Medium |
| E3 | EoP | Vault policy bypass | Medium |
| E4 | EoP | Circuit soundness bug | High without audit |
| E5 | EoP | Signature forgery | Low |
| E6 | EoP | Proof replay | App-dependent |

---

## 10. Priority Focus Areas

Three threats warrant disproportionate attention in implementation and review, because they combine high residual risk with consequences that bypass the architecture's core guarantees.

**Scope grammar agreement (T4)** is the most dangerous threat in the model. Any disagreement between Vault's policy-layer scope parser and the circuit's scope-constraint logic creates an authorization bypass that is invisible under normal operation and only surfaces when an attacker discovers it. Mitigation requires a shared canonical encoding library, exhaustive interoperability testing across the full scope grammar, and a policy of treating the scope encoding as a frozen interface that changes only through coordinated circuit and policy updates.

**Circuit soundness bugs (E4)** have caused multiple real production incidents in the ZK ecosystem. They are hard to detect through normal testing because the bug permits proofs of false statements — there is no failing test case unless the test specifically constructs a false-statement input. Mitigation requires professional audit of any non-trivial circuit, use of well-reviewed circuit libraries where possible, and adversarial testing focused on known soundness-bug patterns (under-constrained field elements, missing range checks, boundary conditions in comparison operators).

**Expired delegation enforcement (E2)** depends on a trustworthy current time being provided as a public input at verification. Off-chain verifiers that accept a prover-supplied timestamp have effectively disabled expiry. Mitigation requires sourcing current time from an authenticated, independent source: on-chain verifiers should use block timestamps; off-chain verifiers should use signed time from a TSA or equivalent, and the signature verification should be part of the verifier's acceptance logic, not the prover's.

---

## 11. Out-of-Scope Items

This threat model does not cover the internal security of Vault itself (Vault operational threat model), the privacy properties of the application's business logic outside proof generation (separate privacy analysis), post-quantum migration planning (separate cryptographic agility analysis), or supply-chain threats against the proving library and its dependencies (separate SCA analysis). These should be treated in dedicated documents and periodically cross-referenced against this model for interaction effects.
