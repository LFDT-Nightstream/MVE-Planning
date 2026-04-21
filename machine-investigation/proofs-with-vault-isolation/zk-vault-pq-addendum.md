# Addendum — Post-Quantum Adaptation

## Adapting the ZK Proving with Vault-Mediated Signature Delegation Architecture for Quantum Resistance

**Status:** Forward-Looking Design Addendum
**Version:** 1.0
**Companion to:** Systems Design Document v1.0, STRIDE Threat Model v1.0, C4 Architecture Document v1.0, C4 Level 4 Code Documentation v1.0, Cost and Schedule Estimate v1.0
**Scope:** Cryptographic agility planning and post-quantum migration path

---

## 1. Purpose and Scope

This addendum extends the reference architecture to address the cryptographic threat posed by cryptographically relevant quantum computers (CRQCs). It is not a drop-in replacement for the base architecture — a naive substitution of post-quantum primitives into the existing design produces a system that is currently intractable to deploy at production scale. Instead, this document analyses the threat precisely, identifies which components need to change and which do not, describes three viable migration paths with their respective trade-offs, and recommends a staged approach that maintains operational viability today while positioning the system for clean transition once post-quantum ZK tooling matures.

The audience for this document is architects and cryptographers planning multi-year roadmaps for systems whose proofs need to remain secure beyond the expected arrival of CRQCs. Teams deploying this architecture for near-term use whose proofs have no forward-security requirement may defer reading this addendum, but should at minimum review section 6 on the harvest-now-decrypt-later threat before concluding that deferral is safe.

The document assumes familiarity with the base architecture and the cryptographic primitives it currently uses. It does not re-derive those foundations. Where a post-quantum alternative is recommended, the recommendation is grounded in the NIST Post-Quantum Cryptography standardisation programme's output as of the document's reference date and in the published literature on post-quantum zero-knowledge systems; readers should verify that the specific primitives recommended here remain current at the time they act on this guidance, because the field is evolving rapidly.

---

## 2. The Quantum Threat, Decomposed

The architecture's cryptographic dependencies fall into three distinct categories, each with a different quantum-threat profile. Treating them together obscures the analysis; treating them separately reveals that the migration is more selective than a full redesign.

### 2.1 Signature Scheme Vulnerability

The signature scheme Vault uses to sign delegations — EdDSA on BabyJubJub, BLS on BLS12-381, or ECDSA on secp256k1, per the base architecture's recommendations — is vulnerable to Shor's algorithm. A sufficiently capable quantum computer running Shor's algorithm can compute the discrete logarithm in the group underlying any of these schemes in polynomial time, recovering the private key `sk_root` from the public key `pk_root`. Once an attacker holds `sk_root`, they can forge arbitrary delegations, generate proofs of arbitrary statements, and the architecture's core security property collapses.

The threat is not speculative in kind — Shor's algorithm exists and works — but is speculative in timing. Estimates of when a CRQC capable of breaking 256-bit elliptic curve cryptography will exist range from roughly a decade to multiple decades, with significant uncertainty and recent progress prompting some analysts to shorten their estimates. Critically, the threat does not require the CRQC to exist today for action to be required today, for reasons discussed in section 6.

### 2.2 Hash Function Vulnerability

The architecture uses hash functions in several places: the canonical encoding of delegation messages typically uses a SNARK-friendly hash (Poseidon, Rescue) for in-circuit efficiency; the proof system's Fiat-Shamir transformation uses a cryptographic hash; the signature scheme's internal hashing uses a conventional hash; and depending on implementation, various integrity checks throughout the system use hashes.

Hash functions are vulnerable to Grover's algorithm, which provides a quadratic speedup over classical brute-force search. This reduces the effective security of an `n`-bit hash to `n/2` bits against a quantum attacker. A 256-bit hash provides 128 bits of post-quantum security, which is generally considered adequate; a 128-bit hash provides 64 bits of post-quantum security, which is not. The mitigation is straightforward: use hash functions with at least 256-bit output wherever post-quantum security is required. Most modern systems already do this, so this threat typically requires minimal architectural change.

Poseidon and Rescue, the SNARK-friendly hashes commonly used in the architecture's canonical encoding, can be parameterised for 256-bit output at modest cost increase; the adjustment is a parameter choice, not an algorithm replacement.

### 2.3 Proof System Vulnerability

The proof system itself has structure-dependent vulnerability that is often overlooked in post-quantum migration planning. Pairing-based SNARKs — Groth16, PLONK over BLS12-381, any construction that relies on bilinear pairings — inherit the discrete-log vulnerability of their underlying curves. A CRQC breaks the pairing-based SNARK's soundness, allowing an attacker to produce proofs of false statements. This is worse than signature forgery because it breaks the verifier's trust in the proof system itself, not merely in one key.

Non-pairing SNARKs have varying status. Halo and Halo2, which use inner-product arguments over elliptic curves, are also discrete-log-dependent and are broken by a CRQC. Bulletproofs have the same issue. STARKs, which use hash-based commitments via Merkle trees, are post-quantum secure by construction — their soundness depends only on the collision resistance of the underlying hash function, which is subject to Grover but not Shor and is addressable by parameter choice. FRI-based proof systems generally share this property.

This is the most significant threat of the three, because changing the proof system is not a parameter adjustment — it cascades into circuit implementation, verifier implementation, and proof size and verification cost characteristics that affect the rest of the architecture.

### 2.4 What Is Not Vulnerable

Several parts of the architecture require no quantum-motivated changes. Vault's storage backend, sealed and unsealed using symmetric cryptography (AES-256), is post-quantum secure with its current parameters subject to the Grover doubling noted above. Transport layer security for communication between components will migrate to post-quantum key exchange through the ongoing broader internet transition; this is a platform concern rather than an architecture concern. The operational structure of the architecture — the trust boundary placement, the delegation pattern, the scope grammar — is independent of which specific cryptographic primitives instantiate it and remains valid under any choice.

This is important because it means the architecture's conceptual contributions — the paradox resolution, the delegation pattern, the scope-bound short-lived credential approach — carry through to the post-quantum world. What changes is the toolbox; what stays is the design.

---

## 3. Why the Naive Substitution Does Not Work

The obvious migration path is to swap each pre-quantum primitive for its NIST-standardised post-quantum replacement: Dilithium or Falcon for the signature, SHA3-256 or SHAKE-256 for the hashes, STARKs for the proof system. At the protocol level this works. At the implementation level, it currently does not produce a deployable system.

The core problem is that in-circuit verification of post-quantum signatures is extraordinarily expensive. Dilithium verification involves polynomial arithmetic over rings that do not align with the native field of common SNARK systems. Estimates of Dilithium verification cost inside a Groth16 or Halo2 circuit run into tens of millions of constraints — orders of magnitude larger than EdDSA-on-BabyJubJub verification, which is already the expensive part of the base architecture's circuit. At tens of millions of constraints, proving time extends from seconds or minutes per proof to hours, proving memory requirements exceed what commodity hardware provides, and the system becomes operationally non-viable for any reasonable request rate.

Falcon is somewhat better-suited to circuit implementation than Dilithium because its structure is closer to lattice operations that can be expressed cleanly in algebraic constraints, but the constraint count is still an order of magnitude higher than pre-quantum equivalents, and Falcon's use of floating-point arithmetic in signing (though not verification) adds implementation complexity.

SPHINCS+, the hash-based signature standard, sidesteps lattice arithmetic but has its own problem: its verification involves a Merkle authentication path over a large tree, and the hash invocations along that path become a circuit-size burden similar to other schemes' algebraic operations. XMSS and related stateful hash-based schemes have additional operational complexity (state management is required for security) that makes them poor fits for this architecture's issuance model.

The situation is not static. Active research into SNARK-friendly post-quantum signatures, including lattice signatures designed for efficient circuit verification and hash-based signatures with optimised Merkle structures, is producing gradual improvements. Specialised proof systems with native support for lattice arithmetic — such as lattice-based SNARKs built directly on the assumed hardness of lattice problems — may eventually provide the efficiency that naive substitution lacks. But as of the reference date of this document, none of these constructions is mature enough for production deployment, and the naive migration path is therefore not currently viable.

The practical consequence is that post-quantum migration of this architecture requires more thought than primitive substitution. The following section describes three paths that are viable, with their respective trade-offs.

---

## 4. Three Viable Migration Paths

### 4.1 Path A — Hybrid Signatures with Classical Circuit Verification

The first viable path preserves the existing circuit structure by having Vault produce two signatures per delegation: a pre-quantum signature (EdDSA on BabyJubJub) that the circuit verifies, and a post-quantum signature (Dilithium or Falcon) that a separate verification layer checks outside the circuit.

Under this scheme, the proof itself attests that the holder had a pre-quantum-valid delegation. Independently, an outer envelope around the proof carries the post-quantum signature, and verifiers check this envelope's signature against the post-quantum root public key. A proof is accepted only if both checks pass. An attacker who breaks the pre-quantum signature can forge the in-circuit check but cannot forge the outer envelope; an attacker who somehow breaks the post-quantum signature but not the pre-quantum signature faces the reverse problem. As long as at least one of the two signature schemes remains secure, the system remains secure.

The advantages of this approach are substantial. Circuit work is unchanged from the base architecture. Proving cost is unchanged. The system remains operationally viable today using current ZK tooling. The transition to post-quantum security happens at the envelope layer, which uses conventional (non-circuit) cryptography where post-quantum signatures are already practical at production scale.

The disadvantages are more subtle but real. The system now requires two signature verifications, doubling Vault's signing cost and the verifier's verification cost. Key management becomes more complex: two root keys must be protected, rotated, and audited. The envelope layer is a new architectural component that must be designed, implemented, and audited; it is the post-quantum equivalent of a message authentication wrapper and carries its own implementation risks. Most importantly, the proof's core soundness still rests on the pre-quantum proof system — an attacker who can run Shor's algorithm can produce a proof of a false statement that the circuit accepts, regardless of whether they can forge the outer signature. The outer envelope protects the *delegation*, not the *proof*.

This path is appropriate for systems that need immediate quantum-resistance improvement while ZK tooling catches up, where the most sensitive threat is delegation forgery rather than proof forgery, and where the operational overhead of dual signatures is acceptable.

### 4.2 Path B — STARK-Based Proof System with Hybrid Signatures

The second path addresses the proof system vulnerability that Path A leaves exposed. It replaces the pairing-based or discrete-log-based SNARK with a STARK, making the proof system itself post-quantum secure, while retaining the hybrid signature approach for the delegation.

Under this scheme, the circuit is reimplemented in a STARK-compatible framework (Winterfell, StarkWare's Cairo, Plonky2 with FRI backend). The circuit still verifies a pre-quantum signature in-circuit for efficiency, but the proof system's soundness no longer depends on any discrete-log assumption. The outer envelope carries a post-quantum signature as in Path A.

The primary advantage is that proof soundness is now post-quantum secure. An attacker with a CRQC can still forge the pre-quantum signature the circuit checks, but to exploit this they must also produce a valid STARK proof of a false statement, which requires breaking the STARK's hash-based commitment scheme — a Grover-weakened but not Shor-broken property.

The disadvantages are proof size and verifier cost. STARK proofs are much larger than SNARK proofs — typically hundreds of kilobytes versus hundreds of bytes — and their verification is correspondingly more expensive. For on-chain verifiers this is a significant cost increase; for off-chain verifiers it is tolerable but not free. The circuit implementation effort is also substantial; STARK tooling is less mature than SNARK tooling, and re-implementing a production circuit in a new framework is a multi-month undertaking for an experienced team.

This path is appropriate for systems where proof soundness matters more than proof size — regulatory or high-assurance deployments where the verifier can afford large proofs, systems that generate proofs at rates low enough that verification cost is not a bottleneck, or systems with strategic need for maximum forward security.

### 4.3 Path C — Full Post-Quantum Native

The third path is the theoretical end-state: a fully post-quantum architecture where the signature scheme, the hash functions, and the proof system are all post-quantum secure, and where the signature verification happens inside the circuit using a post-quantum primitive rather than a hybrid envelope.

This path is not currently deployable at production scale for the reasons discussed in section 3. It is included here as the target end-state toward which Path A and Path B are staging. A realistic arrival window for Path C is three to seven years from the reference date of this document, depending on progress in three independent research fronts: SNARK-friendly post-quantum signatures (or post-quantum signatures with efficient STARK-based verification), proof systems with native support for the algebraic structures underlying post-quantum primitives, and the general maturation of production tooling for those systems.

Teams planning against this path should monitor the research landscape through NIST announcements, IACR conference outputs (CRYPTO, EUROCRYPT, ASIACRYPT, TCC), and the major ZK conference venues (ZK Summit, zkSummit, ZKProof workshop). They should not commit to Path C on a schedule; they should commit to monitoring, and to re-evaluating the migration plan annually against current tooling.

### 4.4 Path Comparison

The three paths are not mutually exclusive — they are stages of a migration. A realistic plan adopts Path A immediately, transitions to Path B when the operational case for larger proofs becomes acceptable or when a high-assurance deployment requires it, and transitions to Path C when tooling matures to support it.

The comparison below summarises the trade-offs:

Path A preserves deployability today. It protects against delegation forgery via the post-quantum envelope but leaves proof soundness resting on a pre-quantum foundation. Implementation effort is modest — roughly 2 to 4 additional person-months on top of the base architecture for the envelope layer, key management extension, and dual-signature verification.

Path B adds proof system post-quantum security. It requires reimplementing the circuit in a STARK framework and accepting larger proofs with higher verification cost. Implementation effort is substantial — roughly 6 to 12 additional person-months for the circuit reimplementation plus the Path A additions, and the team must have or acquire STARK-specific expertise.

Path C is the end-state and is not currently deployable. Implementation effort cannot be meaningfully estimated until tooling matures.

---

## 5. Architectural Changes Required

This section details the specific component-level changes required for each path, organised by the base architecture's component structure.

### 5.1 Vault Cluster Changes

For Path A, Vault's Transit ZK mount is extended to hold two keys: the existing pre-quantum `sk_root` and a new post-quantum `sk_root_pq`. The delegation issuance flow signs each message with both keys, producing a pair `(sig_pq_preq, sig_pq)`. The audit device records both signatures. Policy remains unchanged except for requiring that scope grammars evolve identically under both keys, a discipline enforced by the shared canonical encoding.

For Path B, Vault's Transit mount changes match Path A. No additional Vault changes are required because the proof system change is in the circuit and verifier, not in Vault.

For Path C, Vault holds only `sk_root_pq` and issues a single post-quantum signature. This is the simplest state but is not currently achievable.

Key rotation complexity increases under Path A and Path B because two keys must be rotated in coordination. A rotation protocol that transitions both keys together is required; rotating only one leaves the system reliant on the other alone, which defeats the hybrid protection.

### 5.2 Application Service Changes

For Path A, the `DelegationClient` and `Delegation` classes (documented in the C4 Level 4 artifact) are extended to carry both signatures. The `WitnessBuilder` passes only the pre-quantum signature to the circuit, as in the base architecture. A new `EnvelopeBuilder` component constructs the outer envelope from the proof and the post-quantum signature. The submission client includes the envelope in its submission to the verifier.

For Path B, the application service changes match Path A. The circuit's proof system change is transparent to the application service, which interacts with the prover through an abstract interface that hides whether the underlying proof is a SNARK or a STARK.

For Path C, the application service simplifies — the hybrid-signature handling is removed, and the flow returns to a single signature per delegation. Again, this is the target state.

The shared canonical encoding library must be extended to handle both signature types deterministically. The library is the locus of cross-component agreement (the T4 defence from the threat model) and must evolve in lockstep across Vault, application, and circuit implementations. Adding post-quantum primitives to it is a significant change that should trigger a full audit cycle.

### 5.3 Circuit Changes

For Path A, the circuit is unchanged from the base architecture. This is the entire point of Path A — it delivers quantum-resistance improvement without disrupting the circuit layer.

For Path B, the circuit is reimplemented in a STARK framework. The signature-verification subcircuit, the scope-constraint subcircuit, and the statement subcircuit are all rewritten against the new framework's constraint primitives. The canonical-encoding decoder inside the circuit must produce identical field-element outputs to the Vault-side and application-side encoders, which requires careful attention to how the new framework represents field elements. This reimplementation is the single largest source of Path B's additional effort.

For Path C, the circuit again changes, this time to verify a post-quantum signature inside the circuit natively. The specific implementation depends on which post-quantum signature and which proof system mature first; committing to an implementation approach now is premature.

### 5.4 Verifier Changes

For Path A, the verifier checks two things: the proof (as in the base architecture) and the outer post-quantum signature on the envelope. The verification-key registry publishes both `pk_root` (for in-circuit signature verification via the proof) and `pk_root_pq` (for envelope signature verification). On-chain verifiers face complications here because post-quantum signature verification on-chain is currently expensive — Dilithium verification on Ethereum mainnet, if supported at all, consumes significant gas — and this is one of the operational costs of Path A.

For Path B, the verifier additionally uses a STARK verification algorithm. STARK verification on-chain is an active area of engineering work; on-chain verifiers for STARK proofs exist (StarkWare's L2 is the prominent example) but the cost and complexity are higher than SNARK verifiers. Off-chain STARK verifiers are straightforward.

For Path C, the verifier is unified to a single post-quantum scheme. This simplifies the verifier but, again, is not currently achievable.

### 5.5 Verification-Key Registry Changes

The registry changes are mechanical across all three paths: it publishes the key material required by whichever path is active. The registry's integrity concerns are unchanged, and its signed-publication discipline applies to post-quantum keys exactly as it applies to pre-quantum keys.

One subtlety deserves mention. The registry itself is typically protected by a signature that verifiers use to authenticate its published keys. If that signature is pre-quantum, the registry's authentication becomes the weak link in an otherwise post-quantum system. Upgrading the registry's own signing infrastructure to post-quantum is a prerequisite for meaningful post-quantum security downstream, and this upgrade should be scheduled before the Vault-side upgrade rather than after.

---

## 6. Harvest-Now-Decrypt-Later and the Timing Argument

The most important question for any post-quantum migration plan is "when". The naive answer — "when CRQCs exist" — is incorrect for systems whose proofs attest to facts that remain consequential after the proof is generated.

Consider a system that produces proofs today, stored by a verifier or retained in an audit log. An attacker with network access can record the associated delegations, proofs, and public inputs. Today, these recordings are useless to the attacker — they cannot forge new proofs without `sk_root`, and they cannot retroactively produce different proofs. The attacker waits.

When a CRQC arrives, the attacker uses Shor's algorithm to recover `sk_root` from the now-public `pk_root`. They then retroactively forge signatures for arbitrary delegations, generate proofs for arbitrary statements, and substitute these forged proofs for the original recordings — or, more dangerously, produce forged proofs for statements that were never made but that they can now claim were made. The temporal asymmetry is the threat: the attacker's work happens in the future, but its consequences reach back to proofs produced today.

Whether this matters depends on what the proofs attest. A proof that attests to the correctness of a one-time action whose outcome is already realised (a payment that settled, a credential that was already validated) has limited retroactive exposure — the action cannot be undone by a future forgery. A proof that attests to an identity claim, a historical transaction that remains subject to dispute, a regulatory attestation that might be audited years later, or any fact whose truth has continuing consequence, is fully exposed. A forged proof of an identity credential produced in a post-CRQC world, but claimed to have been produced before the CRQC arrived, is indistinguishable from a legitimate proof without independent out-of-band evidence.

For systems in the latter category, the migration timeline is driven not by when CRQCs arrive but by when the proofs produced today will cease to matter. If the proofs matter for ten years, and CRQCs arrive in ten years, migration must be complete today. If the proofs matter for one year, migration must be complete before CRQCs are one year away — a less aggressive but still non-trivial deadline given the uncertainty in CRQC timing.

The practical recommendation is: inventory the longevity of the proofs the system produces. If any class of proof has consequential lifetime exceeding the optimistic CRQC arrival estimate, begin Path A migration now. If all classes have short consequential lifetime, Path A migration can be deferred but should be planned, because the transition takes time and starting under crisis conditions is much worse than starting under planning conditions.

---

## 7. Cost and Schedule Implications

The cost and schedule document estimates for the base architecture land in the range of $3M to $5M for mainstream deployment over 9 to 15 months. Post-quantum adaptation modifies these estimates as follows.

Path A adds 2 to 4 person-months of engineering effort and 1 to 2 additional months of external audit, translating to roughly $300k to $600k additional cost and 2 to 3 months of additional schedule. This is modest relative to the base project and is the most achievable form of post-quantum readiness on a reasonable budget.

Path B adds 6 to 12 person-months of engineering effort (primarily from circuit reimplementation) and 2 to 4 additional months of external audit, translating to roughly $1M to $2M additional cost and 4 to 6 months of additional schedule. This is a significant increment and should be planned as a major project phase rather than treated as an extension to the base architecture.

Path C cannot be cost-estimated meaningfully until tooling matures. Teams should monitor the landscape and produce Path C estimates only when the required primitives have reached the same maturity level that current pre-quantum primitives had when the base architecture's estimate was produced.

Ongoing operational cost increases modestly under Path A and Path B due to the additional Vault signing cost, the additional verifier cost, and the audit refresh burden of a more complex cryptographic configuration. A reasonable estimate is 10% to 20% increase in ongoing annual cost for Path A, and 20% to 40% for Path B, over the base architecture's ongoing cost.

These estimates assume a team that has completed the base architecture. Teams attempting to deploy Path A or Path B from the outset, without first deploying the base, should not compress the schedule by treating the post-quantum work as concurrent with the base work — the base work depends on cryptographic decisions that the post-quantum work revises, and attempting both concurrently introduces serialisation that the sequential approach avoids.

---

## 8. Recommended Roadmap

The recommended approach for most organisations deploying this architecture is a staged migration aligned with the maturity of the ZK post-quantum ecosystem.

**Stage 1 — Crypto-agility foundation (during base architecture delivery).** The base architecture is delivered using pre-quantum primitives, as documented. During this delivery, the team designs the architecture for cryptographic agility: the shared canonical encoding library is structured to accommodate multiple signature types, the `Delegation` and `Witness` classes are designed to carry variable signature payloads, the Transit mount configuration accommodates future additional keys, the verification-key registry's publication format supports multiple algorithms. This foundational work adds modest effort during the base project and substantially reduces later migration cost. It should not be skipped even by teams that do not plan immediate post-quantum migration.

**Stage 2 — Path A deployment (12 to 24 months after base production launch).** Once the base architecture is stable in production, the organisation deploys Path A: hybrid signatures with the existing circuit. This provides meaningful post-quantum improvement for delegation forgery protection while preserving the proof-generation pipeline's performance characteristics. The audit cycle for Path A is somewhat simplified because the base architecture's audit has already established trust in the pre-quantum components.

**Stage 3 — Monitor and re-evaluate annually.** After Path A deployment, the organisation monitors the post-quantum ZK ecosystem annually. Key signals include NIST updates on standardised primitives, publication of production-ready SNARK-friendly post-quantum signature schemes, maturation of STARK tooling for non-specialised use cases, and the emergence of proof systems designed natively for post-quantum arithmetic. Re-evaluation is deliberate, not reactive — tying it to an annual cadence ensures it happens even when no crisis prompts it.

**Stage 4 — Path B deployment (timing dependent on ecosystem).** When STARK tooling reaches the maturity currently enjoyed by SNARK tooling, and when the organisation's operational constraints permit larger proofs and higher verification cost, the team reimplements the circuit in a STARK framework. This transition is multi-month and should be planned as a major project in its own right. In high-assurance deployments the timing may be accelerated; in cost-sensitive deployments it may be deferred.

**Stage 5 — Path C deployment (multi-year horizon).** When the full post-quantum native architecture becomes deployable — signature-friendly post-quantum primitives, efficient in-circuit verification, mature proof systems — the organisation consolidates onto a single post-quantum signature and retires the hybrid envelope. The simplification is operationally valuable and the system reaches its end-state post-quantum configuration.

This roadmap is deliberately conservative. It prioritises operational continuity, staged risk reduction, and alignment with external ecosystem maturity over aggressive migration timelines. Organisations with specific threat models — intelligence agencies, high-stakes financial infrastructure, or systems with very long-lived proofs — may justify acceleration, and that acceleration should be grounded in their specific analysis rather than in a general preference for haste.

---

## 9. What Remains the Same

A final observation worth emphasising: the architectural contributions of the base design persist through all three migration paths. The paradox resolution — that proving against a Vault-held secret requires restructuring what the circuit proves rather than releasing the secret — is independent of which cryptographic primitives instantiate the scheme. The delegation pattern — short-lived, scope-bound credentials issued by Vault for specific proving sessions — translates directly into the post-quantum setting. The trust boundary placement, the separation between the application service and the prover, the canonical encoding discipline, the audit integration, the scope grammar, the key rotation protocols — all remain valid.

This is the sense in which the base architecture is post-quantum ready despite using pre-quantum primitives: its design is sound; only its cryptographic instantiation needs to evolve. Teams that deploy the base architecture now are not locked into pre-quantum cryptography; they are positioned for migration. The effort described in this addendum is the migration work, not a replacement of the underlying architectural thinking.

This positioning is one of the architecture's quiet strengths. A design whose security properties depended on specific primitives — as many historical designs have — would require reconsideration from scratch in the face of the quantum threat. The signature-delegation pattern's independence from the specific signature scheme, combined with the proof system's replaceability behind a stable semantic contract, means the architectural work already done retains its value. The investment in the base design compounds rather than deprecates.

---

## 10. Summary

Post-quantum adaptation of this architecture is achievable but requires more thought than primitive substitution. The naive approach of replacing each pre-quantum primitive with a NIST-standardised post-quantum equivalent produces a system whose circuit is currently too expensive to deploy at production scale. Three viable migration paths exist: a hybrid signature approach that improves post-quantum readiness without circuit changes, a hybrid-plus-STARK approach that additionally makes the proof system post-quantum secure at the cost of larger proofs, and a full post-quantum native end-state that is not currently deployable but is the target for a multi-year roadmap.

The recommended plan is to deliver the base architecture now with crypto-agility foundations, deploy Path A 12 to 24 months after production launch, monitor ecosystem maturity annually, and transition to Path B and eventually Path C as tooling permits. For systems whose proofs have long consequential lifetime, the migration timeline is driven by proof longevity rather than by CRQC arrival estimates; the harvest-now-decrypt-later threat makes deferral more expensive than it first appears.

The architectural contributions of the base design — the paradox resolution, the delegation pattern, the scope grammar, the trust boundary placement — are independent of the specific primitives used and carry through unchanged to the post-quantum world. The base architecture is not a pre-quantum design that needs replacement; it is a sound design that needs cryptographic re-instantiation.

Cost and schedule impact is modest for Path A (roughly $300k to $600k additional, 2 to 3 months additional schedule), substantial for Path B (roughly $1M to $2M additional, 4 to 6 months additional schedule), and not yet estimable for Path C. These increments are planned phases of a multi-year program, not immediate project scope, and organisations should not inflate their current project estimates to cover Path B or Path C until the relevant decisions are ready to be made.
