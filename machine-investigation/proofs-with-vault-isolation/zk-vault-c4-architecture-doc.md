# C4 Architecture Document

## Zero-Knowledge Proving with Vault-Mediated Signature Delegation

**Status:** Reference Architecture
**Version:** 1.0
**Companion to:** Systems Design Document v1.0, STRIDE Threat Model v1.0
**Methodology:** C4 Model (Context, Container, Component) by Simon Brown

---

## 1. Introduction

This document presents the signature-delegation ZK proving architecture in the C4 model's three levels of abstraction. Each level serves a different audience and answers a different question. The Context diagram answers "what is this system and who uses it" and is appropriate for executives, product stakeholders, and anyone new to the domain. The Container diagram answers "what are the major deployable parts" and is appropriate for platform and operations teams, security reviewers, and new engineers joining the project. The Component diagram answers "what lives inside each deployable and how do they interact" and is appropriate for the implementation team and for detailed security review.

The C4 model nominally includes a fourth level — Code — that decomposes individual components to class or function granularity. This document intentionally stops at the Component level. ZK proving circuits do not have the procedural structure that Code-level C4 assumes; they are systems of algebraic constraints rather than call graphs, and their correct representation is a constraint-system diagram (R1CS, custom gates, lookup tables) that belongs alongside the C4 hierarchy rather than inside it. Readers interested in the circuit internals should consult the circuit specification document, which uses the appropriate idioms.

This document assumes familiarity with the systems design document's description of the overall architecture and the threats it addresses. Where the C4 diagrams reveal structural choices that warrant explanation — particularly choices that diverge from what a naive reading of the systems document might suggest — those choices are called out with their rationale.

---

## 2. Level 1 — System Context

### 2.1 Purpose of the Context Diagram

The Context diagram places the ZK proving system within its environment. It treats the entire proving system as an opaque box and shows only the people and external systems that interact with it across its outer boundary. The purpose is to establish scope: everything inside the box is the team's responsibility, everything outside is a dependency or a consumer.

### 2.2 Actors and External Systems

The Context diagram identifies two human actors and three external systems.

The **end user** is the person whose real-world action motivates a proof. Depending on the deployment, this might be a wallet user authorising a transaction, an employee presenting a credential, a service account invoking a privileged operation, or any principal on whose behalf the system generates a proof. The end user's interaction with the proving system is typically mediated by a client application, but at the Context level this mediation is not yet visible — what matters is that a real person's intent initiates the flow.

The **security operator** is the person responsible for governing the system's authorisation policy and reviewing its audit trail. This is a distinct role from the end user because the security operator's concerns are architectural and forensic rather than transactional. They approve scope grammars, review delegation issuance patterns, investigate anomalies, and authorise key rotations. Calling them out as a first-class actor at the Context level is a deliberate choice: governance is not a developer concern to be handled later, it is a primary use case that shapes the architecture.

The **identity provider** is the external system that authenticates callers. In enterprise deployments this is typically an existing IdP (Okta, Azure AD, Auth0), in cloud-native deployments it might be a Kubernetes API server issuing ServiceAccount tokens, and in workload-identity deployments it might be SPIFFE/SPIRE or a cloud IMDS. The identity provider is external because it predates and outlives the proving system; identity origination is not a capability the proving system builds, it is a capability the proving system consumes. Vault is shown at the Container level as the component that *validates* identity assertions from the IdP, not as the IdP itself.

The **time authority** is the external system that provides trustworthy current time. This is elevated to a first-class external system because the architecture's expiry enforcement depends on it. On-chain verifiers can use block timestamps from their own chain, off-chain verifiers need signed time from a Time Stamp Authority or equivalent trusted source. Treating time as an implementation detail is a recurring source of vulnerabilities in systems with similar structure; promoting it to the Context diagram ensures it receives design attention rather than being assumed.

The **verifier** is the external system that consumes proofs. It is external because the proving system does not own it — the verifier might be a blockchain smart contract owned by a consortium, a service operated by a counterparty organisation, a public verifier anyone can run, or an internal service in a different team's estate. The proving system's job ends when it produces a proof; what happens to that proof is outside its scope. This separation is enforced by the verifier holding only the public verification key and public inputs, not any secret material from the proving side.

### 2.3 Interactions

The end user submits a request to the proving system; this request expresses an intent that requires cryptographic authorisation. The security operator reviews audit data flowing from the proving system and issues policy updates in the reverse direction. The proving system authenticates callers via the identity provider, fetches signed time from the time authority, and submits proofs to the verifier. None of these interactions carry secret material across the boundary — authentication carries tokens, time carries signed timestamps, proof submission carries only public outputs.

### 2.4 What is Deliberately Not in the Context Diagram

The Vault cluster is not shown at Context level even though it is arguably the most important part of the architecture. This is correct for C4: Vault is an internal component of the proving system from the outside world's perspective. An external observer does not care whether the proving system uses Vault, an HSM, a cloud KMS, or a bespoke key management layer; they care only that the system's contract is upheld. Surfacing Vault at Context level would confuse implementation with interface.

The proof itself is not shown as a data asset flowing between entities. Proofs are the medium of the interaction between proving system and verifier; at Context level the interaction itself is the noun, not its payload.

---

## 3. Level 2 — Containers

### 3.1 Purpose of the Container Diagram

The Container diagram opens the proving system box and shows the major deployable units inside. In C4 terminology, a container is anything that executes independently — a process, a pod, a serverless function, a database, a static artifact store. The Container diagram is the primary communication artifact for platform and operations teams because it corresponds most directly to what they operate.

### 3.2 Containers Inside the Proving System

The proving system decomposes into five containers.

The **Vault cluster** is the trust root. It stores the root signing key `sk_root`, enforces authentication and authorisation policy for delegation requests, performs signing operations in-process, and writes immutable audit records. The Vault cluster is shown as a single container at this level even though it is typically deployed as a multi-node cluster for high availability; the clustering is a Vault-internal concern that does not change the architectural contract.

The **application service** is the container that interacts with end users and orchestrates the proving flow. It receives user requests, handles their authentication, generates ephemeral session keys, requests delegations from Vault, assembles witnesses, dispatches proving work, and submits proofs to verifiers. It is the logical centre of the system and typically the largest container by code volume, but it is explicitly *not* trusted with root key material — a point reinforced by its separation from both Vault and the prover.

The **prover service** is a separate container whose sole responsibility is running the proving library. Separating it from the application service is a deliberate architectural choice discussed below. It receives a witness and a circuit identifier from the application service over a local authenticated channel, computes the proof, returns it, and zeroises the witness from memory.

The **verification-key registry** is a signed artifact store that publishes the verification key `vk` for each deployed circuit and the public key `pk_root` for the active Vault signing key. Verifiers fetch from this registry rather than receiving keys through some out-of-band channel. Treating verification-key distribution as its own container acknowledges that this is a first-class integrity concern with established tooling (Sigstore, signed container registries, on-chain contract deployments with governance) and that collapsing it into "shared configuration" invites the class of vulnerabilities where verifiers accept proofs against a silently-rotated verification key.

The **audit sink** is an external append-only store — typically a SIEM — to which Vault streams audit events in near real time. It is shown inside the proving system's boundary because the architecture depends on its availability and integrity for forensic reconstruction, but it is commonly operated by a central security team rather than the proving system's team.

### 3.3 Why the Application Service and Prover Service Are Separate Containers

This separation is the most consequential structural choice at the Container level and warrants explicit justification. The two could technically run in a single process; some reference implementations do exactly this. The architecture recommends separation for four reasons.

Proving is computationally heavy and bursty; a single proof may consume multiple CPU cores for seconds to minutes. Application request handling is I/O-bound and steady. Co-locating them forces a single scaling and sizing policy that is wrong for both workloads. Separation permits horizontal scaling of provers independently from the application, and permits placing provers on CPU-optimised hardware while the application runs on standard compute.

The prover's attack surface becomes much narrower when it has no external network exposure. In the separated design, the prover receives input only from the application service over a local channel — a Unix domain socket with peer credential verification, or mTLS within a single pod. It has no HTTP endpoint, no authentication logic, and no parsing of end-user input. This means vulnerabilities in HTTP stacks, authentication libraries, and input parsers cannot reach the prover, even though those libraries are present in the application service.

The forensic reasoning after a compromise is much cleaner when the prover is isolated. If the application service is compromised, investigators need to determine what witnesses the attacker could have caused the prover to process. With the prover separated, this is answered by reading the local channel's access logs: a finite, bounded set of inputs. With the prover co-located, the same question becomes "what did the compromised process do to its own memory", which is unanswerable in general.

The separation aligns with the threat model's trust decomposition. The application service is in the untrusted zone by design — it holds delegations and session keys, and its compromise is explicitly bounded and tolerated. The prover service has a strictly narrower role and a strictly shorter-lived set of secrets (the witness exists in its memory only during proof generation). Modelling this as two containers lets each be reasoned about independently; modelling it as one forces the weaker trust assumption onto the combined unit.

### 3.4 Interactions at the Container Level

The end user submits a request to the application service. The application service authenticates to Vault, requests a delegation, and receives the signed delegation back. It forwards witness data to the prover service over the local channel and receives a proof. It fetches signed time from the external time authority (shown at Context level) for inclusion in public inputs. It submits the proof to the external verifier.

Vault streams audit events to the audit sink continuously. The verification-key registry receives signed `vk` publications during circuit deployment from a separate operational flow (not shown as a container-level interaction because it is an out-of-band deployment event, not a runtime flow). Verifiers pull `vk` and `pk_root` from the registry; this pull happens during verifier initialisation and occasionally thereafter, not per-proof.

### 3.5 Container-Level Availability and Failure Modes

Vault unavailability halts new delegation issuance but does not halt in-flight proving against unexpired delegations. The application service is typically deployed redundantly and should tolerate single-instance failure. The prover service is horizontally scaled and work-queue driven; individual prover failures cause individual proof retries, not system failure. The verification-key registry is read-mostly and can be served from a cache or CDN; its unavailability affects new verifier initialisation but not existing verifiers. The audit sink's unavailability is a compliance incident but does not halt proof generation; Vault buffers audit events up to a configured limit.

---

## 4. Level 3 — Components

### 4.1 Purpose of the Component Diagram

The Component diagram decomposes the two most design-dense containers — Vault and the application service — into their internal components. A component at this level is a logical grouping of functionality with a clear responsibility and interface, typically corresponding to a module, package, or plugin in the implementation. The Component diagram is the primary communication artifact for the implementation team because it defines the division of responsibilities and the internal contracts each team member is building against.

The other three containers (prover service, verification-key registry, audit sink) are shown as single boxes at this level because their internal structure is either determined by third-party software (Vault audit devices, Sigstore for the registry) or too implementation-specific to standardise (the prover service's internals depend on which proving library and which circuit). Teams building those containers should produce their own component-level documentation as internal artifacts.

### 4.2 Components Inside the Vault Cluster

The Vault cluster exposes four components relevant to delegation issuance.

The **auth method** component validates caller authentication. In a typical deployment this is Vault's AppRole method backed by Kubernetes ServiceAccount JWT validation or cloud workload identity, configured to exchange a workload-bound token for a Vault token with a short TTL. The auth method's output is an authenticated identity that subsequent components can reason about. It does not itself issue delegations; it only establishes who is asking.

The **policy engine** component evaluates the authenticated caller's entitlement to the specific delegation request. It enforces scope allowlists (this caller may request scopes A and B but not C), rate limits (this caller may request at most N delegations per minute), and TTL bounds (this caller's delegations may not exceed T minutes). The policy engine is the architectural location where operational security policy is enforced; it is a configuration surface, not a code surface, and should be managed through policy-as-code workflows with review and drift detection.

The **Transit ZK mount** component performs the actual signing. It is a dedicated Vault Transit secrets engine mount whose key material is `sk_root` in a ZK-friendly algorithm (EdDSA on BabyJubJub, BLS on BLS12-381, or similar). Isolating this to its own mount — separate from any general-purpose Transit mount the organisation runs — serves three purposes: it prevents policy confusion between ZK signing and conventional signing, it permits differing key rotation schedules, and it gives auditors a single mount to monitor for ZK-specific anomalies. The Transit ZK mount receives a canonical delegation message from upstream components and returns the signature; it does not itself construct the message, a point that matters for the shared-encoding discussion below.

The **audit device** component writes an immutable record of each delegation issuance, including the authenticated caller, the requested and granted scopes, the issued expiry, and a timestamp. It is configured to forward records to the external audit sink in near real time, typically through a streaming protocol with acknowledgement semantics that permit Vault to buffer on downstream unavailability. The audit device is shown with a dashed connection to upstream components because it runs in parallel with the signing path rather than sequentially — the response to the caller does not wait for audit forwarding to complete, but audit commitment does occur before Vault releases the signature to the caller.

### 4.3 Components Inside the Application Service

The application service decomposes into six components organised around the proving pipeline.

The **request handler** is the component that receives incoming requests from end users, validates their format, and extracts the caller's intent. It produces a structured representation of what the caller wants proved, which downstream components will use to determine the required scope. The request handler is also where business-logic authorisation happens — checks that are application-specific rather than cryptographic, such as "this user owns this resource" or "this operation is permitted for this account type". It is explicitly separated from the delegation components because business-logic authorisation should not be conflated with cryptographic delegation.

The **session key manager** generates a fresh ephemeral keypair `(sk_session, pk_session)` at the start of each proving session. The implementation uses a cryptographically secure random number generator seeded from the operating system's entropy source. The session key manager is responsible for the keypair's entire lifecycle: generation, handoff to downstream components, and zeroisation after proof submission. Reusing a session keypair across sessions is explicitly prohibited — every session has its own pair, and the correspondence between session and keypair is what ties a proof to a specific delegation.

The **delegation client** authenticates to Vault and requests a delegation. Its inputs are the scope specification (derived from the request handler's intent) and `pk_session` (from the session key manager). Its output is the signed delegation returned by Vault. The delegation client is the only component in the application service that has outbound network access to Vault; isolating this responsibility lets network policy be written narrowly (only this component's egress reaches Vault) and lets the Vault client library's attack surface be contained. The delegation client also handles retries, rate limit backoff, and Vault token lifecycle, which are operational concerns rather than cryptographic ones.

The **witness builder** assembles the inputs to the proving circuit. Its inputs are the delegation signature (from the delegation client), `sk_session` (from the session key manager), the scope parameters (reconstructed from the delegation request), and the public statement (from the request handler). Its output is a structured witness object ready to pass to the prover.

**The witness builder uses a shared canonical-encoding library.** This is the single most important implementation detail in the entire architecture, and the Component diagram makes it visible precisely because it is invisible at higher levels. The canonical encoding of the delegation message must be computed identically by three parties: the component that prepares the message for Vault to sign, the witness builder that prepares the message for in-circuit verification, and the circuit itself. Any disagreement between any two of these opens the scope-bypass vulnerability discussed as T4 in the threat model. The architectural mandate is that a single library — a single source file, compiled into every party that needs it — implements this encoding, and that any change to the encoding is a coordinated release across all three parties. Teams that treat this as "obvious byte-layout code" and rewrite it independently in each language end up with the class of vulnerabilities that are nearly undetectable under normal operation.

The **prover orchestrator** passes the witness to the prover service over the local authenticated channel, receives the resulting proof, and returns it to the calling flow. It also handles prover-service unavailability (retry, fallback to a different prover instance) and timeout semantics (proving that exceeds a configured bound is aborted and reported as a failure rather than allowed to run indefinitely).

The **submission client** delivers the proof and public inputs to the verifier. Its implementation depends on the verifier type: for on-chain verifiers it constructs a transaction and submits it to a node, for off-chain verifiers it makes an authenticated HTTP call. It is separated from the prover orchestrator because the submission contract varies across verifier deployments while the proving flow does not; isolating this variance behind a single component simplifies the rest of the pipeline.

### 4.4 Cross-Container Interactions at the Component Level

The interaction between the application service and Vault is not a single call; it is a sequence of calls mediated by the delegation client on one side and the auth method, policy engine, Transit ZK mount, and audit device on the other. The delegation client first exchanges its workload-bound token for a Vault token (auth method), then submits the delegation request (policy engine, Transit ZK mount), then receives the signed delegation. The audit device records the outcome on the Vault side; no corresponding audit record is produced on the application side at this point, because the application's audit interest is in proofs generated rather than delegations requested.

The interaction between the application service and the prover service is a local call from the prover orchestrator to the prover service's entry point, carrying the witness. This call uses a local authenticated channel (Unix domain socket with SO_PEERCRED, or mTLS within a pod network) rather than general network access. Both ends verify the peer's identity; neither end accepts witnesses or proofs from unauthenticated peers.

### 4.5 Why the Witness Builder is a Distinct Component

Placing witness assembly in its own component might look like over-decomposition — it could plausibly live inside the prover orchestrator or be inlined into the delegation client. The architecture keeps it separate for two reasons specific to this domain.

The witness builder is the locus of the canonical-encoding library's use, and making it a distinct component makes the encoding's correctness reviewable in isolation. Code review of "does the witness builder produce byte-identical output to the in-circuit decoder and Vault's signed message" is a finite, bounded review task when it is a single component. Dispersing the encoding across multiple components makes the same review effectively impossible.

The witness builder is the boundary between authorisation-layer concerns (delegations, session keys, scopes) and proving-layer concerns (field elements, circuit inputs, public/private classification). These are conceptually different kinds of data and invite different kinds of bugs; isolating the translation between them in one component makes both sides cleaner.

---

## 5. What the C4 Model Does Not Capture

The C4 model is a structural decomposition. It does not natively represent several concerns that are important to this architecture and that readers should seek in companion documents.

Dynamic behaviour — the sequence of events in a successful proving flow, the sequence in a failing flow, the retry and backoff semantics — is better expressed as a sequence diagram or state machine, not as C4. The systems design document's Data Flow section (§5) covers the nominal sequence; detailed failure-mode sequences should be added as flow diagrams if the implementation team needs them.

Trust boundaries are not first-class in C4. The diagrams in this document use dashed bounding rectangles to indicate the Vault trust boundary and the application-service grouping, but this is a convention layered on top of C4 rather than a C4 construct. The STRIDE threat model is the authoritative source on trust boundaries and what crosses them.

Data classification — what data is sensitive, what is public, what is private — is not directly shown. The Component diagram's narration describes which components handle which data, but a formal classification table is a separate artifact and should be produced as part of the system's compliance documentation.

Cryptographic structure — the circuit's constraint system, the signature scheme's internals, the hash function's algebraic form — is deliberately out of scope for C4, which abstracts over implementation detail at every level. These are documented in the circuit specification and cryptographic protocol documents.

---

## 6. Using These Diagrams

The three C4 diagrams in this document are intended to be the primary architectural reference for the system, used in different situations by different audiences.

The Context diagram is the right artifact to open any architecture review, onboarding session, or stakeholder briefing. It orients the audience before any detail is introduced and establishes the scope of the conversation. Time spent on the Context diagram at the start of a meeting is rarely wasted.

The Container diagram is the right artifact for deployment planning, operational runbook design, capacity planning, and security architecture review at the deployment-topology level. It is also the appropriate diagram for cross-team coordination conversations, because each container typically has a single owning team.

The Component diagram is the right artifact for implementation planning, code review structuring, and detailed security review of the delegation and proving flows. It is not a diagram for stakeholders; it is a working artifact for the engineers building the system.

A common failure mode in using C4 is treating the three levels as a linear document to be read top-to-bottom. They are better understood as three different lenses on the same system, each appropriate for different purposes. A reader seeking to understand "how do we scale the prover" should go directly to the Container diagram; forcing them through the Context diagram first is wasted time. A reader seeking to understand "why is the witness builder its own component" should go directly to the Component diagram. The document's structure supports non-linear access.

---

## 7. Evolution and Maintenance

C4 diagrams go out of date when the system evolves and the diagrams do not. The diagrams in this document should be updated whenever any of the following changes: a new external system becomes a dependency or consumer (Context diagram), a new container is introduced or the responsibilities of existing containers change (Container diagram), or components are added, removed, or substantially renamed (Component diagram).

The discipline that keeps these diagrams useful is including a diagram review as part of the change management process for changes that touch architecture. A pull request that adds a new external integration should be expected to update the Context diagram; a pull request that splits a container should update the Container diagram. Treating the diagrams as living artifacts maintained alongside the code — rather than documentation that is produced once and decays — is the only approach that keeps them accurate over a multi-year system lifetime.

The companion documents (systems design, STRIDE threat model) should be reviewed alongside any diagram change, because structural changes to the architecture frequently imply changes to the threat model or to the component specifications in the systems document. A change that passes review in isolation but contradicts a companion document is a signal that the companion document also needs updating, not that the companion document can be ignored.
