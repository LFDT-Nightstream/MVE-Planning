# Cost and Schedule Estimate

## Production Embodiment of the ZK Proving with Vault-Mediated Signature Delegation Architecture

**Status:** Planning Estimate
**Version:** 1.0
**Companion to:** Systems Design Document v1.0, STRIDE Threat Model v1.0, C4 Architecture Document v1.0, C4 Level 4 Code Documentation v1.0
**Audience:** Engineering leadership, project sponsors, finance partners
**Estimate class:** Class 4 / ROM (Rough Order of Magnitude) — ±50% expected accuracy before scoping exercise

---

## 1. Purpose and Limits of This Estimate

This document provides a cost and schedule estimate for producing a production embodiment of the ZK proving architecture documented in the companion artifacts. It is intended to support early-stage planning conversations: sizing the opportunity, deciding whether to proceed, allocating initial budget, and commissioning the scoping work that would produce a firmer estimate.

An estimate at this stage is a sizing exercise, not a commitment. The companion documents describe a reference architecture, not a specified product. At least a dozen decisions with significant cost and schedule implications remain open, and several of them can move the total by factors of two to four. Those decisions are enumerated in section 7, and until they are made, any single-number answer to "how much will this cost and how long will it take" is misleading — it either hides assumptions that will later be contested, or it brackets such a wide range that it fails to support decision-making.

Readers should treat the ranges in this document as planning inputs, not as commitments. Treating them as commitments has two predictable failure modes: the project is approved against the low end of the range and later requires repeated scope re-negotiations when reality lands higher, or the project is approved against the high end and carries more budget than it needs at the expense of alternative investments. Both failures are avoidable by running the scoping exercise described in section 9 before committing to a number.

The estimate is structured to be transparent about where the ranges come from. Each work stream is sized with its own rationale, so readers can challenge specific assumptions rather than arguing with a summary number. Where a decision dominates the range, that dependency is called out inline rather than buried in footnotes.

---

## 2. Effort Decomposition by Work Stream

The project decomposes into seven work streams, each with distinct skill requirements, parallelisation characteristics, and sources of estimate uncertainty. The streams are not strictly sequential; many can overlap, and the schedule in section 4 reflects the realistic parallelism.

### 2.1 Cryptographic Protocol Design

This stream covers finalising the signature scheme choice, specifying the canonical encoding, designing the scope grammar, defining the trusted-setup procedure if one is needed, and documenting the key rotation protocol. It is the foundational design work on which all other streams depend.

Effort estimate: **2 to 4 person-months of senior cryptographer time**. The range reflects the variance between organisations that have a cryptographer on staff who is already familiar with the problem domain (lower end) and organisations that must engage an external cryptographer who needs to understand the application context before producing design output (upper end).

This stream cannot be meaningfully accelerated by adding people. It is bottlenecked by design review cycles and by the individual cryptographer's ability to get the details right. Attempting to parallelise it by splitting the work across multiple cryptographers produces integration artifacts that take additional time to reconcile. The stream must complete — at least for the canonical encoding and scope grammar — before circuit implementation can usefully begin, making it the critical path's starting point.

The deliverables are a signature scheme specification, a canonical encoding specification with test vectors, a scope grammar specification with conformance tests, a key rotation protocol, and a cryptographic rationale document suitable for audit review. These are concrete artifacts that can be inspected before committing to implementation, which makes this stream a natural gating milestone.

### 2.2 Circuit Implementation

This stream covers writing the proving circuit, including the signature-verification subcircuit, the scope-constraint subcircuit, the statement subcircuit, and the canonical-encoding decoder that reconstructs the signed message in-circuit.

Effort estimate: **4 to 8 person-months for an experienced ZK team, 8 to 16 person-months for a team learning ZK on this project**. The doubling is realistic and should not be optimised away. Circuit work has pathologies that do not appear in conventional software — constraint-system under-constraint bugs that pass all functional tests but permit forgery, field-arithmetic pitfalls that look correct to a general cryptography engineer, proof-system-specific gotchas that accumulate only through prior project experience.

The stream is hard to parallelise beyond two engineers because the constraint system is tightly coupled and must be reasoned about as a whole. A third engineer typically adds communication overhead without reducing elapsed time, unless that engineer is dedicated to a separable subsystem (the verification contract, the proving harness) rather than the circuit itself.

An external audit of this stream's output is effectively mandatory for production deployment; circuit soundness bugs have caused multi-million-dollar incidents in deployed systems, and they are difficult to detect through normal testing because the bug permits proofs of false statements — there is no failing test case unless the test specifically constructs a false-statement input.

### 2.3 Application Service and Delegation Client Implementation

This stream covers the twelve classes documented in the Code-level artifact, the shared canonical-encoding library, and the integration with Vault. It is the largest stream by code volume but the best-understood from a conventional software engineering perspective.

Effort estimate: **3 to 5 person-months for a team of two to three mid-to-senior engineers**. The range reflects variance in testing discipline — teams that invest heavily in the cross-consumer tests for the canonical encoding library (the primary defence against the T4 scope-grammar-disagreement threat) land at the upper end of the range, and that investment is the correct choice.

This stream can start in parallel with circuit work once the canonical encoding and scope grammar are specified. The dependency flows from cryptographic protocol design (stream 2.1) through both this stream and circuit implementation (stream 2.2); the two downstream streams can then proceed concurrently with periodic synchronisation points to ensure the shared encoding library remains byte-identical across consumers.

### 2.4 Prover Service Integration

This stream covers selecting a proving library, wiring it into the prover service container, tuning for the target hardware, and building the inter-container transport to the application service.

Effort estimate: **1 to 2 person-months**. The range reflects the difference between a straightforward integration with a well-documented library (lower end) and a deployment where the chosen library requires significant adaptation work for the target environment — GPU acceleration, memory-constrained environments, specialised hardware such as FPGAs for production-scale provers (upper end).

This stream depends on the circuit being substantially complete. Attempting it earlier produces integration work that gets discarded as the circuit evolves. It is typically the shortest stream on the critical path and rarely dominates scheduling.

### 2.5 Vault Configuration, Policy, and Audit Integration

This stream covers the Transit mount setup, policy-as-code authoring, audit device configuration, SIEM forwarding, backup and disaster recovery integration, and the operational runbooks that the team operating the system will use.

Effort estimate: **1 to 2 person-months of platform-engineering time**. The range is driven almost entirely by whether Vault is already operated at production scale in the organisation. If so, this stream is largely configuration work against an existing operational capability; if not, this stream grows significantly as the underlying Vault deployment must be established first. In the latter case the estimate should be increased to 3 to 5 person-months and the dependency explicitly acknowledged.

This stream typically runs in parallel with application-service work and rarely drives the critical path. Its output — working Vault configuration, signed-off policy, tested audit forwarding — is a prerequisite for integration testing but not for application-service implementation.

### 2.6 Verification-Key Registry and Verifier Deployment

This stream covers the registry infrastructure that publishes verification keys and `pk_root`, the signing pipeline that ensures registry integrity, and the verifier deployment itself — whether that is an on-chain smart contract, an off-chain service, or both.

Effort estimate: **1 to 3 person-months**. On-chain verifiers dominate the upper end of the range because gas optimisation becomes significant engineering work, contract governance must be designed and implemented, and deployment carries real operational cost (the deployment transaction gas at production gas prices is typically tens of thousands of dollars for a non-trivial verifier, and deployment mistakes require redeployment). Off-chain verifiers are closer to standard service deployment and sit at the lower end.

Cross-chain verification — deploying verifiers on multiple chains, or using cross-chain messaging to propagate proof results — adds another 1 to 3 person-months and should be estimated as a separate stream if in scope.

### 2.7 Security Audit, Penetration Testing, and Remediation

This stream covers the external cryptographic audit of the circuit and protocol, a code audit of the implementation, penetration testing of the deployed system, and the remediation cycle that invariably follows.

Effort estimate: **3 to 6 elapsed months from audit kickoff to production sign-off, plus 1 to 2 person-months of internal engineering time for audit response and remediation**. External audit cost typically runs **$150k to $500k** for the cryptographic and code audit, with penetration testing adding **$50k to $150k** on top. These ranges assume reputable auditors; engaging lower-cost providers is possible but materially increases the risk of missing the kinds of bugs the audit exists to find, and in practice the cost of an incident dwarfs the savings from a cheaper audit.

This stream does not run purely at the end of the project. The most effective audit engagements begin with a protocol review once cryptographic protocol design (stream 2.1) is complete, a mid-project check-in once core circuit work is substantially done, and the full code-and-deployment audit once integration is complete. Spreading the audit engagement across the project catches issues earlier, when they are cheaper to fix, and reduces the risk that late-stage audit findings force major rework.

Remediation always surfaces issues. Budgeting zero remediation time is unrealistic; budgeting a month and hoping for less is reasonable for a well-executed project, and two months is appropriate if the team is learning the domain.

---

## 3. Total Effort and Team Composition

### 3.1 Aggregate Effort

Summing the engineering streams gives **15 to 30 person-months of implementation effort** before audit and remediation. Adding audit remediation and production hardening brings the total to **20 to 40 person-months** of engineering time, plus the external audit cost enumerated in section 2.7.

This is raw engineering effort. It does not include project management, product ownership, technical writing, internal communications, operations handoff, training, or the various overheads that a real organisation incurs. A reasonable overhead factor for these concerns in a mature engineering organisation is 20% to 40% on top of the raw engineering estimate, pushing the full loaded effort to **24 to 56 person-months**.

Organisations with lighter overheads (small teams, informal processes, minimal cross-functional coordination) land at the lower end. Organisations with heavier overheads (formal change management, multiple stakeholder review cycles, regulatory documentation requirements) land at the upper end, and specific regulatory regimes can push the overhead beyond this range.

### 3.2 Team Composition

A realistic team shape for this work, at peak staffing, consists of approximately eight people. One senior cryptographer works part-time on the project, heavy at the start during protocol design and again during audit response, lighter during steady-state implementation. Two circuit engineers focus on the proving circuit, its tests, and the in-circuit canonical-encoding decoder. Two to three application engineers build the application service, delegation client, and prover service integration. One platform engineer handles Vault configuration, audit integration, and the verification-key registry. One technical lead or staff engineer owns the architecture, coordinates across streams, and handles the integration points between streams that fall between engineers' primary responsibilities.

The team does not need to be at peak staffing for the entire project duration. The realistic staffing curve starts with two to three people (the cryptographer, the technical lead, and an early platform engineer) during protocol design, ramps to full staffing by month three or four once parallel work is possible, remains at peak through the implementation phase, and tapers during audit remediation and production rollout.

### 3.3 Skill Availability as a Planning Risk

The team composition above assumes the relevant skills are available. Circuit engineering is the skill most likely to be scarce in an organisation that has not previously shipped a ZK system, and the market for experienced ZK engineers is tight. Options for addressing a skills gap include hiring (slow, 3 to 6 months to fill a senior ZK role), contracting (faster but expensive at $300 to $500 per hour for senior circuit engineers, and produces knowledge transfer debt), partnering with a ZK-focused consultancy (effective but expensive and often requires sharing architectural control), or training internal engineers (cheapest in dollars but doubles circuit work duration and requires external audit to catch soundness bugs the learning team will not catch in review).

No option is strictly better; the right choice depends on the organisation's strategic intent. Organisations that view ZK as a one-time capability should prefer partnering; organisations that view it as a long-term capability should prefer hiring and training; organisations with urgent deadlines should combine contracting for immediate execution with hiring for long-term capability.

---

## 4. Schedule

### 4.1 Nominal Schedule

With the team shape and effort estimates above, the end-to-end schedule from project start to production launch is typically **9 to 15 months**, broken roughly as follows.

**Months 1 through 3** cover protocol design, initial circuit scaffolding, and foundational platform work. The cryptographer completes the canonical encoding, scope grammar, and signature scheme specifications. The circuit engineers begin the signature-verification subcircuit. The platform engineer stands up Vault configuration. The application engineers begin the shared canonical-encoding library and the session key manager.

**Months 3 through 7** cover parallel circuit, application, and platform work. The circuit team completes the full circuit and its test suite. The application team implements the twelve classes of the application service and the six classes of the delegation client. The platform engineer completes Vault policy, audit, and verification-key registry work. The cryptographer shifts to part-time, supporting design questions as they arise.

**Months 7 through 9** cover integration and internal testing. All streams converge on a working end-to-end pipeline. Cross-consumer tests for the canonical encoding library run against the circuit, the application, and Vault message preparation. End-to-end proofs are generated and verified. Performance tuning begins.

**Months 8 through 11** overlap the end of implementation with the external audit engagement. The audit team reviews the protocol design (completed earlier), the circuit implementation, and the code and deployment configuration. Issues are reported, triaged, and fed back into the implementation teams for remediation.

**Months 10 through 13** cover audit remediation and production hardening. Critical and high-severity audit findings are remediated. Operational runbooks are finalised. Monitoring dashboards are built. Disaster recovery procedures are tested. Staff training is completed.

**Months 12 through 15** cover production rollout with progressive traffic shifting. Initial deployment runs alongside the existing system if applicable, with a small fraction of traffic shifted to the new system. Monitoring confirms behaviour matches expectations. Traffic share increases over weeks to months until the new system handles production volume.

### 4.2 Schedules That Are Not Realistic

Aggressive timelines below 9 months are achievable only with an experienced ZK team working on a well-understood variant of this architecture, and even then typically skip some combination of circuit optimisation, comprehensive audit, or operational polish. Organisations with a strong ZK track record have delivered similar systems in 6 to 8 months; organisations attempting this for the first time have not, regardless of how compressed the project plan appears on paper.

Timelines above 15 months usually indicate one of three things: a team learning ZK from scratch (in which case 18 to 24 months is honest), novel cryptographic construction requiring original research (which should be separated from production engineering and treated as a research project with its own estimate), or organisational constraints — compliance review cycles, procurement timelines, multi-stakeholder approval processes — that are real and legitimate but should be separated from the engineering estimate so that the parts are independently debuggable.

### 4.3 Critical Path and Buffer

The critical path for the nominal schedule runs through cryptographic protocol design, circuit implementation, integration, audit, and remediation. The streams that run off the critical path — Vault configuration, verification-key registry, prover service integration — have float and can absorb delays without affecting the end date.

Schedule buffer should be applied at the end of the critical path rather than distributed through it. A typical production-grade project reserves 15% to 25% of the total schedule as end-of-project buffer to absorb audit findings, production rollout surprises, and the inevitable late-surfacing issues. Projects that apply the buffer per-stream typically consume it per-stream without reducing overall risk; projects that reserve it at the end typically release some of it if risks don't materialise.

---

## 5. Budget

### 5.1 Cost Ranges

Fully-loaded cost, including salaries, benefits, infrastructure, external services, and organisational overhead, typically falls in the following ranges for a US-based engineering organisation.

A **lean execution** — experienced team, off-the-shelf cryptographic primitives, off-chain verifier, minimal but credible audit scope — comes in at **$1.5M to $2.5M** total. This range is appropriate for organisations with existing ZK experience, existing Vault infrastructure, and a use case that does not require exotic cryptographic choices. It is the floor for a credible production deployment; lower numbers typically reflect either undercounting or cutting corners that will surface as post-production incidents.

A **mainstream realistic execution** — mixed-experience team, on-chain verifier, comprehensive audit — comes in at **$3M to $5M** total. This range is appropriate for most organisations considering this architecture for the first time, with moderate complexity in the use case and a serious commitment to production operation. It is the most common range for first-time deployments of this architecture class.

A **conservative or high-assurance execution** — novel circuit work, multi-party audit for high-value deployment, regulatory engagement, formal verification of critical circuit components — comes in at **$5M to $10M or higher**. This range is appropriate for financial infrastructure, identity systems at national scale, or other deployments where the cost of a single incident exceeds the incremental cost of stronger assurance. In this tier the audit spend alone can exceed $1M and the timeline often extends beyond 15 months.

### 5.2 Breakdown of a Mainstream Estimate

A representative $4M estimate within the mainstream range breaks down approximately as follows. Engineering salaries and benefits for a team of six to eight over twelve months, fully loaded, account for $2.5M to $3M. External cryptographic and code audit costs $300k to $500k. Penetration testing costs $75k to $125k. Infrastructure and tooling during development costs $50k to $100k. Contingency for unspecified items — consultant engagements, additional audit rounds, late-surfacing scope — accounts for the remaining $250k to $500k.

This breakdown will shift significantly based on the decisions enumerated in section 7. An on-chain verifier can add $100k+ in deployment and governance costs; a compliance engagement can add $200k+ in consulting and documentation; a formal verification engagement for a critical circuit can add $500k or more depending on scope.

### 5.3 Cost Drivers

The single biggest cost driver within these ranges is **security audit scope**. A $150k audit that finds three critical bugs saves more than its cost by avoiding incidents whose remediation and reputational costs easily run into the millions. A $500k multi-firm audit sequence is appropriate for systems where the proof's integrity protects high-value assets — financial settlements, identity credentials, regulatory attestations — and where the cost of a bug exceeds the incremental audit cost by orders of magnitude. Under-investing in audit is the most expensive savings available and should be explicitly resisted during budget negotiation.

The second biggest driver is **team experience**. A team that has shipped a production ZK system before will complete the work in the lower half of the schedule range and the lower half of the cost range. A team learning ZK on this project should expect the upper half of both, plus the risk of requiring external circuit-engineering consultancy at $300 to $500 per hour to unblock specific challenges. The cost differential between experienced and learning teams, once training time and consultancy are accounted for, typically runs $500k to $1M on a mainstream-scale project.

The third driver is **regulatory and compliance scope**, discussed in section 7. Regulated deployments extend the timeline by 3 to 6 months and add $200k to $1M in compliance-specific cost; the variance is driven by the specific regulatory regime and the organisation's existing compliance infrastructure.

The fourth driver is **verifier deployment target**. On-chain verifiers require gas optimisation, governance design, and deployment infrastructure that off-chain verifiers do not. The difference runs $100k to $500k depending on the target chain and the verifier's complexity.

---

## 6. Ongoing Operational Cost

The estimate above covers project delivery. Ongoing operation of the deployed system incurs additional annual cost that should be accounted for in total-cost-of-ownership conversations.

Infrastructure cost depends on deployment scale but typically runs $50k to $500k annually for mainstream deployments, driven by prover compute (proving is expensive), Vault cluster operation, and verifier hosting or on-chain gas costs. High-volume deployments can see substantially higher infrastructure cost, and the prover compute component scales roughly linearly with proof volume.

Staffing cost for ongoing operation typically requires 1 to 2 engineers at steady state — an application engineer who owns the service and a platform engineer who handles Vault, audit, and infrastructure concerns. This is 1 to 2 person-years annually at a fully-loaded cost of roughly $300k to $600k.

Audit refresh cost recurs. Ongoing changes to the circuit, the scope grammar, or the cryptographic protocol should trigger refresh audits. A reasonable budget is $100k to $300k annually for ongoing audit work, scaling with the pace of changes.

Total ongoing cost for a mainstream deployment typically runs $500k to $1.5M annually. This should be factored into the business case alongside the project delivery cost; a project with $4M delivery cost and $1M annual operation has a three-year total cost of $7M, not $4M.

---

## 7. Decisions That Dominate the Estimate

Before committing to any of the numbers above, the following decisions should be made, because each can move the total by 30% or more.

**Proof system choice.** Halo2 and PLONK avoid per-circuit trusted setup but have larger proof sizes and verifier costs. Groth16 has tiny proofs and cheap verifiers but requires per-circuit trusted setup, which adds operational complexity and a one-time ceremony cost. STARKs avoid both trusted setup and pairing-based cryptography but produce much larger proofs. This choice ripples into circuit work, verifier work, and operational process. Organisations deploying on chains with strict proof-size limits (most L1 blockchains) face pressure toward Groth16 despite the setup cost; organisations with more flexibility should consider Halo2 for the operational simplicity.

**Signature scheme choice.** EdDSA on BabyJubJub inside a BN254 SNARK is the cheapest option and is well-supported by current libraries. BLS on BLS12-381 is even cheaper inside pairing-friendly systems and offers aggregation properties that some use cases benefit from. ECDSA on secp256k1 is expensive inside a SNARK — hundreds of thousands of constraints versus thousands for EdDSA — but is mandatory if the verifier is the Ethereum mainnet without an additional wrapping layer. Choosing the right scheme early can reduce circuit cost by an order of magnitude and correspondingly reduce circuit engineering time.

**Verifier deployment target.** An on-chain verifier on Ethereum involves gas optimisation, governance design, and the cost of the deployment transaction itself. An off-chain verifier is a standard service deployment. Cross-chain verification — proving once and verifying on multiple chains — adds another architectural layer and should be separately estimated.

**Regulatory and compliance scope.** If the system handles regulated data (financial transactions, healthcare data, identity credentials, personal data under GDPR or similar), audit scope expands to include compliance dimensions, documentation requirements expand substantially, and schedule extends by 3 to 6 months for compliance review cycles. If the system operates outside regulated domains, the engineering schedule dominates and the project timeline matches the estimates in section 4.

**Team experience.** Discussed in section 5.3. The differential between an experienced and learning team is significant enough to be called out as its own decision point, because the strategic question of whether to build internal capability versus engage external expertise is typically a leadership decision rather than a tactical one.

**Existing infrastructure.** If the organisation already operates Vault at production scale, has SIEM infrastructure, has established signed-artifact CI/CD pipelines, and has integrated identity provider plumbing, much of the platform-engineering stream is already done. Greenfield deployments add 2 to 4 months of foundational infrastructure work and proportional cost.

**Statement complexity.** The architecture's core circuit — signature verification plus scope enforcement — is well-characterised. The statement subcircuit, which proves the application-specific claim, varies enormously in complexity across use cases. A statement that proves "amount ≤ cap" is a few constraints; a statement that proves a complex business rule or a cryptographic operation can be thousands or millions of constraints. This decision is often under-appreciated in early planning because it requires application design work that postdates the architectural decision; the recommendation is to produce a first-draft statement specification during the scoping exercise and use it to anchor the circuit size estimate.

---

## 8. Risk Register

Several risks can materially affect cost or schedule and warrant explicit tracking throughout the project.

**Circuit soundness bug surfaces late.** A soundness bug discovered during audit or post-production is expensive to fix, because it typically requires circuit changes, verifier changes (which may require on-chain governance cycles), and re-audit. Mitigation: engage audit early, invest in differential testing across canonical encoding consumers, budget explicit remediation time. Residual risk: medium to high, depending on circuit complexity and team experience.

**Canonical encoding disagreement between consumers.** The T4 threat from the STRIDE model materialises as a bug where Vault's message, the application's witness, and the circuit's decoder disagree. This is invisible under normal testing and only surfaces under adversarial conditions. Mitigation: single shared library with exhaustive cross-consumer tests, called out as a first-class engineering discipline in the Code-level documentation. Residual risk: medium; the mitigation is effective but requires sustained discipline.

**Skill availability shortfall.** If the skills gap identified in section 3.3 is larger than estimated, the project's circuit-engineering stream extends and becomes the binding constraint on the schedule. Mitigation: identify the gap explicitly in the scoping exercise, commit to a sourcing strategy before project kickoff rather than discovering the gap mid-project. Residual risk: medium to high if the gap is not addressed during scoping, low if it is.

**Audit finding forces architectural rework.** An audit finding that cannot be addressed through implementation changes — for instance, a protocol-level vulnerability that requires a different signature scheme — forces the project back to protocol design and substantially resets the schedule. Mitigation: engage audit on the protocol specification before circuit implementation begins, so protocol-level findings surface early rather than after implementation investment. Residual risk: low if early-engagement audit is used, medium if audit is deferred to the end of the project.

**Regulatory scope expands during project.** A change in regulatory interpretation, the addition of a new regulated use case, or the expansion of the project's scope into regulated territory can add compliance burden mid-project. Mitigation: clarify regulatory scope during scoping, engage compliance partners early if any regulatory relevance is plausible. Residual risk: domain-dependent; high for financial services and healthcare, low for internal tooling.

**Operational readiness lag.** The deployed system cannot go live until the operating team is trained, runbooks are validated, and incident response procedures are tested. Projects that treat this as an afterthought typically discover it adds 2 to 4 months to the effective launch date despite the engineering being complete. Mitigation: staff the operating team during implementation, not after. Residual risk: medium; the mitigation is simple but frequently ignored.

---

## 9. Recommended Next Step: Scoping Exercise

The most useful next action is not committing to a number from the ranges above, but running a two-to-four-week scoping exercise to collapse the ranges into a committable estimate.

The scoping exercise should produce the following concrete artifacts. A signed-off proof system and signature scheme selection, with rationale documented against the use case's requirements. A first-draft scope grammar specification, produced in collaboration with the application team and the cryptographer, sufficient to estimate circuit size to within 30%. A statement specification describing the application-specific claim the circuit will prove, at a level of detail sufficient for circuit sizing. A decision on verifier deployment target, including whether on-chain, off-chain, or both are required, and which chains if applicable. A regulatory scope assessment identifying whether the system falls within any regulated domain and what specific compliance requirements apply. A team-composition plan validated against internal staff availability, with external sourcing decisions made for any gaps. An existing-infrastructure inventory identifying which of Vault, SIEM, identity plumbing, and CI/CD for signed artifacts is already operational.

With those artifacts produced, the ranges in this document typically narrow to a ±25% estimate suitable for budget approval and a ±2-month schedule estimate suitable for roadmap commitment. The scoping exercise itself typically costs $50k to $150k in engineering and cryptographer time, plus any external cryptographer engagement, and should be treated as a small investment that substantially de-risks the larger project estimate.

Attempting to commit without this scoping work produces one of two failure modes. Either the estimate contains implicit assumptions that later get contested (producing mid-project scope arguments that damage delivery and trust), or the estimate is made so wide that it fails its purpose of supporting planning. Neither failure mode is acceptable for a project of this scale and importance; the scoping exercise is the cheapest way to avoid both.

---

## 10. Summary

Producing a production embodiment of the ZK proving architecture documented in the companion artifacts requires roughly **20 to 40 person-months of engineering effort** plus external audit, deployed over a schedule of **9 to 15 months** by a team of **six to eight people at peak**, at a fully-loaded cost of **$1.5M to $10M** depending on execution choices. The mainstream mid-range estimate is **$3M to $5M over 12 months**, appropriate for most organisations' first deployment of this architecture class.

These ranges are planning inputs, not commitments. Several decisions — proof system, signature scheme, verifier target, regulatory scope, team sourcing, existing infrastructure, statement complexity — can each move the total by 30% or more, and the ranges cannot narrow until those decisions are made. A two-to-four-week scoping exercise, costing $50k to $150k, typically produces the decisions needed to narrow the estimate to ±25% on budget and ±2 months on schedule.

The recommendation is to fund the scoping exercise, use its output to produce a committable estimate, and make the project approval decision against that firmer estimate rather than against the planning ranges in this document. The scoping exercise is a small investment that substantially de-risks the larger project, and organisations that skip it routinely experience the failure modes section 9 identifies: mid-project scope arguments on the one hand, or over-budget buffers on the other.

This document will be superseded by the output of that scoping exercise. In the interim it provides the sizing and rationale needed to decide whether the project is worth scoping in the first place.
