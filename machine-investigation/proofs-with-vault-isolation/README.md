# ZK Proving with Vault-Mediated Signature Delegation

A reference architecture for generating zero-knowledge proofs against secrets managed by HashiCorp Vault, without the root secret ever leaving Vault's process boundary.

---

## The problem this solves

Zero-knowledge proof systems require the plaintext witness as input to the proving circuit. HashiCorp Vault exists specifically to prevent plaintext secrets from leaving its boundary. A naive integration — pulling the secret out of Vault into an application container to run a prover — fully defeats Vault's security model.

This architecture resolves the paradox by restructuring what the circuit proves. Rather than proving knowledge of the root secret, the circuit proves knowledge of a short-lived, scope-bound signature issued by Vault. The root key never leaves Vault. The delegation that does leave is deliberately narrow in authority and short in lifespan, such that its compromise is operationally containable. The resulting proof verifies against Vault's root public key, so any verifier can confirm the proven statement was authorised by the root authority — without the root authority ever having been exposed.

---

## Who this is for

This documentation is written for three audiences, each served by different parts of the set.

Engineering leadership and program sponsors, evaluating whether to commit to a deployment of this class, should start with the infographic and the cost and schedule document. These establish the shape, size, and cost of the commitment before any technical detail is required.

Security architects and cryptographers, evaluating whether the architecture is sound for their threat model, should read the systems design document and the STRIDE threat model. These establish the security properties, the assumptions underlying them, and the residual risks that operations must manage.

Implementation teams, building the system, should work through the C4 architecture document, the C4 Level 4 code documentation, and the post-quantum addendum. These establish the structural and class-level decisions, the invariants each component must uphold, and the forward-looking cryptographic agility requirements.

All three audiences should read the post-quantum addendum eventually. The cryptographic agility foundations it describes must be laid during initial implementation, not retrofitted later.

---

## Contents of this documentation set

The documentation comprises seven artifacts, each serving a distinct purpose. They are listed here in the order a newcomer typically reads them, not in order of importance — every artifact is load-bearing for its audience.

**1. Systems Design Document** introduces the architecture at a narrative level. It states the paradox, explains the core insight that resolves it, specifies each component, traces the data flow, analyses the security properties, and compares the design against alternative approaches (trusted execution environments, multi-party computation). It is the authoritative reference for the architecture's conceptual shape and should be read first by anyone needing to understand why the system looks the way it does.

**2. STRIDE Threat Model** enumerates threats against the architecture using the six STRIDE categories: spoofing, tampering, repudiation, information disclosure, denial of service, elevation of privilege. Each threat is rated by residual risk after architectural mitigations are applied, and the model highlights three priority focus areas where risk most heavily depends on implementation discipline rather than cryptographic strength. This document is the authoritative input to security review cycles and to ongoing threat modelling as the system evolves.

**3. C4 Architecture Document** presents the architecture in the C4 model's three levels: Context, Container, and Component. Each level serves a different audience — executives, platform teams, implementers — and each diagram is accompanied by rationale explaining the structural choices, particularly the ones that diverge from what a naive reading might suggest. The document explains why it deliberately stops at Component level rather than producing a fourth Code-level decomposition of the proving circuit.

**4. C4 Level 4 Code Documentation** completes the C4 hierarchy for the procedural parts of the architecture — the application service and the delegation client. It documents each class's responsibilities, invariants, collaborators, and test coverage, and it consolidates cross-cutting concerns (secret lifecycle, error handling, observability) into a single section. The proving circuit is deliberately not covered here, for reasons explained in the document itself.

**5. Cost and Schedule Estimate** sizes a production embodiment of the architecture. It decomposes the work into seven streams with rationale for each, provides team composition and schedule guidance, and identifies the six decisions that most strongly move the estimate. The document is explicit about its own limits — it is a rough order of magnitude sizing exercise, not a commitment — and recommends a two-to-four-week scoping exercise as the cheapest path from planning-range estimates to committable numbers.

**6. Infographic** distils the six-document set onto a single page, organised as a visual summary suitable for executive briefing or stakeholder circulation. It covers the paradox, the architecture at a glance, the cost and schedule ranges, the team shape, the decisions that dominate the estimate, the top risks, and the recommended next step. It is not a substitute for the underlying documents but a navigation aid and a shareable artifact for conversations where the full documentation set would overwhelm.

**7. Post-Quantum Addendum** extends the architecture for the threat posed by cryptographically relevant quantum computers. It decomposes the quantum threat into its three components (signature scheme, hash functions, proof system), explains why naive primitive substitution does not currently work, and describes three viable migration paths with their respective trade-offs. The addendum argues that the architecture's conceptual contributions are independent of specific cryptographic primitives and therefore survive migration intact — only the instantiation changes.

Five supporting diagrams accompany these documents: three C4 diagrams (Context, Container, Component) and two Code-level diagrams (application service class structure, delegation client class structure). These live alongside the C4 documents and should be read together with them.

---

## How the documents relate

The documents form a layered reference. Higher layers establish what and why; lower layers establish how. When the layers disagree, the higher layers win — the implementation exists to serve the architecture, not the other way around.

The systems design document establishes the architectural contract. Every other document respects that contract. If a STRIDE threat, a C4 decomposition, or an implementation class appears to require a deviation from the systems design, either the systems design needs updating (and the other documents will follow) or the deviation is wrong. This discipline prevents the common failure mode where implementation choices accumulate until the documented architecture no longer describes the deployed system.

The STRIDE threat model and the C4 architecture document are peers, addressing orthogonal concerns (security versus structure) against the same design. Changes to the design should be reviewed against both.

The C4 Level 4 documentation refines the Component-level view with implementation detail. It does not introduce new architectural decisions; it specifies how the Component-level decisions are realised in code. Class-level changes that imply component-level change are flagged as component-level events and reviewed against the C4 architecture document.

The cost and schedule document and the infographic are communication artifacts, not specifications. They do not bind the design; they communicate it. Updates to the design propagate into them rather than the other way around.

The post-quantum addendum is forward-looking. It does not modify the base architecture — it extends it. Teams implementing the base architecture today should read the addendum to understand what agility foundations must be laid now to enable later migration, but need not implement the addendum's migration paths until the ecosystem justifies it.

---

## What this documentation does not cover

Several adjacent concerns are deliberately out of scope and should be documented separately by teams implementing the architecture.

The proving circuit's internal design is not covered at Code-level. Circuits are constraint systems and should be documented using constraint-system idioms — R1CS listings, custom gate specifications, lookup table schemas, constraint-count budgets. A dedicated circuit specification document belongs alongside this set for any team implementing the circuit.

The Vault cluster's internal configuration is referenced but not specified. Vault is HashiCorp's product with its own extensive documentation; this set describes how the architecture uses Vault, not how Vault itself is deployed and operated. Teams should produce their own Vault deployment runbook grounded in HashiCorp's operational guidance.

The prover service's internal structure is not specified because it depends on the chosen proving library (arkworks, halo2, circom-compatible frameworks, StarkWare's stack). Teams should document their chosen library's integration as an internal artifact.

Deployment topology, monitoring dashboards, incident response procedures, and operational runbooks are out of scope. These are operational documents that should be produced by the team operating the system, informed by but distinct from the architecture documentation.

Application-specific business logic — what the system proves, who is authorised to request proofs, how the system integrates with upstream identity and downstream audit — is out of scope. This documentation covers the proving infrastructure; the application using it is a separate design.

Integration with specific wallet or credential ecosystems (such as the OpenWallet Foundation's projects) is discussed conversationally in the conversation that produced this set but is not formalised in a dedicated document. Teams integrating with such ecosystems should produce an integration document specific to their target.

---

## Recommended reading paths

Four reading paths serve different starting points.

**The executive path** reads the infographic, skims the summary section of the cost and schedule document, and reads the systems design document's introduction and executive summary. Total time: under an hour. Output: an informed decision about whether to commission scoping work.

**The architect's path** reads the systems design document in full, the STRIDE threat model, and the Context and Container levels of the C4 architecture document. It then reads the post-quantum addendum. Total time: four to six hours across multiple sessions. Output: enough understanding to lead architectural review, commission implementation, and make the decisions enumerated in the cost document's decision catalogue.

**The implementer's path** assumes the architect's path has been completed, then reads the Component level of the C4 architecture document, the full C4 Level 4 code documentation, and revisits the STRIDE threat model with implementation in mind. Total time: eight to twelve hours across multiple sessions. Output: enough understanding to implement the system correctly, including the invariants that individual code review cannot establish.

**The auditor's path** reads the systems design document, the STRIDE threat model in full, the cryptographic sections of the post-quantum addendum, and the C4 Level 4 code documentation focusing on the classes flagged as load-bearing against specific threats (particularly those addressing the T4 canonical-encoding threat). Total time: six to ten hours across multiple sessions. Output: enough understanding to conduct a code and design audit, identify residual risks, and produce findings traceable to specific architectural commitments.

All four paths converge on the recommended next step, which is running the scoping exercise described in section 9 of the cost and schedule document.

---

## Status and versioning

All documents in this set are at version 1.0 and reflect a consistent design at a single point in time. They should be versioned together: a change to the systems design document that ripples into the threat model and the C4 documents should produce coordinated updates across all three, not an isolated edit to one.

The discipline that keeps the set useful is including documentation review as part of the change management process for any change that touches the architecture. A pull request that changes a class should be expected to update the Code-level documentation. A pull request that changes a component should update the C4 architecture document. A pull request that changes the design should update the systems design document and cascade through the others. Treating the documentation as a living artifact maintained alongside the code — rather than documentation produced once and then decaying — is the only approach that keeps it accurate over a multi-year system lifetime.

Version bumps should follow semantic-version principles applied to documentation: patch versions for typo and clarification edits, minor versions for additions that do not change existing content, major versions for changes that existing readers would need to re-read.

---

## A closing note on what makes this architecture distinctive

The architecture does not introduce new cryptography. Every primitive it uses — EdDSA signatures, Poseidon hashes, Groth16 or Halo2 proof systems, Vault's Transit engine — is well-established and widely deployed. Its contribution is architectural rather than cryptographic: it demonstrates that the apparent paradox between ZK proving and strict secret management is not a real contradiction, that restructuring what the circuit proves dissolves the paradox, and that the resulting system admits a clean trust boundary, a coherent audit story, and a verification semantics that requires no trust in the prover's environment.

This distinction matters for how the documentation is used. A cryptographically novel architecture would demand formal proofs and peer review of the cryptography itself; this architecture demands formal reasoning about the composition of well-understood primitives and careful implementation discipline around the invariants the composition requires. The documents in this set reflect that focus: they are light on cryptographic derivation and heavy on invariants, trust boundaries, and implementation discipline.

The work of deploying this architecture is, accordingly, mostly engineering discipline rather than cryptographic research. Teams that treat it as the former tend to succeed; teams that treat it as the latter tend to over-invest in the cryptography and under-invest in the invariants that actually determine the system's security in practice.
