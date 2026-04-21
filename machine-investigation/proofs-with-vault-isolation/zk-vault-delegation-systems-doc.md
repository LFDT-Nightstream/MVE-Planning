# Zero-Knowledge Proving with Vault-Mediated Signature Delegation

## Systems Design Document

**Status:** Reference Architecture
**Version:** 1.0
**Scope:** Production ZK proof generation against secrets managed by HashiCorp Vault

---

## 1. Executive Summary

This document describes a reference architecture for generating zero-knowledge (ZK) proofs in systems where the underlying secret material is managed by HashiCorp Vault. It resolves a fundamental tension between two otherwise incompatible security requirements: ZK proof systems require the plaintext witness as an input to the proving circuit, while Vault's entire value proposition rests on ensuring that plaintext secrets never leave its process boundary.

The architecture resolves this tension not by weakening either requirement, but by restructuring what the circuit proves. Rather than proving knowledge of the root secret itself, the proving circuit proves knowledge of a short-lived, scope-bound signature issued by Vault. The root key never leaves Vault. The credential that does leave is deliberately narrow in authority and short in lifespan, such that its compromise is tolerable. The ZK proof is cryptographically bound to Vault's root public key, so any verifier can confirm that the proven statement was authorized by an entity in possession of a valid Vault-issued delegation, without learning which delegation, which caller, or any private data.

This pattern is the recommended default for production ZK systems integrating with Vault. Alternative patterns based on Trusted Execution Environments (TEEs) or Multi-Party Computation (MPC) are discussed as fallbacks for cases where the statement's semantics genuinely require root-key material inside the circuit.

---

## 2. Problem Statement

### 2.1 The Cryptographic Paradox

ZK proof systems such as Groth16, PLONK, Halo2, and STARKs operate by compiling a statement into an arithmetic circuit and producing a succinct cryptographic argument that a prover knows a private input (the *witness*) satisfying that circuit. The witness must be supplied in plaintext to the proving algorithm. There is no known technique for generating a valid proof from an encrypted or secret-shared witness without the proving process at some point reconstructing the plaintext.

HashiCorp Vault, conversely, is designed around the principle that plaintext secret material must never cross its process boundary. Secrets are sealed at rest, unsealed only within Vault's memory, and exposed to clients exclusively through narrow, audited API surfaces. Encryption and signing operations are performed inside Vault so that raw key material stays inside Vault.

A naive integration between these two systems retrieves the secret via Vault's API, places it in the memory of an application container, and feeds it to a proving library. This fully defeats Vault's purpose. The secret now lives in container memory, possibly in core dumps, possibly in logs, possibly readable by the container orchestrator, possibly readable by the hypervisor, and possibly readable by the cloud operator. Every threat Vault was deployed to mitigate has been reintroduced.

### 2.2 Requirements

A correct resolution must satisfy the following requirements simultaneously:

The root key material governed by Vault must never exist in plaintext outside Vault's process memory. Any credential that does exist outside Vault must have a bounded blast radius, defined by scope and expiry, such that its compromise is containable. The proving circuit must produce a sound proof that is verifiable by any party holding only public parameters. The verifier must be able to confirm that the proof was authorized by the root authority without requiring any trust in the prover's operational environment. Audit trails must permit forensic reconstruction of what authority any given proof was generated under.

### 2.3 Non-Requirements

This architecture does not attempt to prove arbitrary statements about the root key's bits. It assumes that the authorization semantics of the proved statement can be expressed as a scoped delegation, which is true for the overwhelming majority of production use cases (identity assertions, transaction authorizations, credential presentations, access control proofs) but is not universally true.

---

## 3. Architecture Overview

### 3.1 Core Insight

The architecture replaces the circuit's witness from `sk_root` to `(sig, sk_session, scope)`, where `sig` is a signature produced by Vault over a structured delegation message. The circuit no longer proves "I know the root key." It proves "I hold a valid signature, produced by the holder of the root key, authorizing this specific statement within a specified scope, and I hold the session key referenced in that signature."

This reformulation is semantically equivalent for almost all real use cases. A user proving "I am authorized to spend from this account" does not actually require the account's root key inside the circuit; they require proof that the account's controller authorized this specific spend. A system proving "this credential was issued by a trusted authority" does not require the authority's signing key inside the circuit; it requires proof of a valid signature from that authority. In both cases, the restructured circuit produces a proof with the same end-user meaning while keeping the root key out of the proving environment.

### 3.2 Trust Boundary

The trust boundary in this architecture encloses Vault alone. The Vault process, its unseal keys, its storage backend, and its auth methods constitute the trusted zone. Everything else — the application container, the proving host, the orchestrator, the network, the verifier — is untrusted. This is a significantly smaller and better-defined trust boundary than the TEE-based alternative, which must additionally trust the hardware vendor's attestation infrastructure and the correctness of the enclave runtime.

Outside the boundary, the worst a full compromise of the application container achieves is theft of a scope-bound, time-bound delegation credential. The attacker cannot forge new delegations (they lack `sk_root`), cannot extend the credential's scope (it is signed), cannot extend its expiry (it is signed), and cannot pivot to other resources (the scope binds them). The credential's exposure window is the TTL, typically measured in minutes.

---

## 4. Component Specification

### 4.1 Vault Configuration

Vault is configured with a dedicated Transit secrets engine mount whose sole purpose is issuing ZK delegations. Separating this from general-purpose Transit mounts is strongly recommended because the signature algorithm and key policy for ZK delegations differ from conventional signing use.

The signing key at this mount is generated in a ZK-friendly algorithm. EdDSA over a SNARK-friendly curve such as BabyJubJub or JubJub is the preferred choice for most proof systems based on BN254 or BLS12-381, because the curve arithmetic is native to the proving field and verification inside the circuit costs on the order of thousands of constraints rather than millions. BLS signatures over BLS12-381 are even cheaper inside a pairing-friendly SNARK and should be preferred when the proof system supports them. ECDSA over secp256k1 is supported but expensive inside a SNARK (hundreds of thousands of constraints) and should be avoided unless the verifier is a system such as Ethereum's EVM that mandates it.

Vault policy on this mount enforces per-caller rate limits, scope whitelisting, and maximum TTL. Policies should be written such that the scope language passed in the delegation request is validated against an allowlist before signing, preventing a compromised application from requesting delegations outside its legitimate authority.

The audit device attached to this mount should be tuned for high-integrity retention, since the audit log becomes the authoritative record of what authority each proof in the system was generated under. Forensic reconstruction depends on this record being complete and tamper-evident.

### 4.2 Delegation Credential Format

The delegation is a signature over a canonical, unambiguous encoding of a structured message. The message contains:

A session public key `pk_session`, which binds the delegation to a specific ephemeral keypair. A scope specification, expressed in a rigid grammar that the circuit can evaluate. An expiry timestamp, expressed in a format the circuit can compare against a public-input current time. A nonce or sequence number, to prevent replay. An optional caller identity, if the verifier wishes to surface it.

Canonical encoding is critical. Any ambiguity in the encoding creates a signature forgery opportunity. The recommended approach is a length-prefixed concatenation of fixed-width fields, or a hash-to-field commitment computed with a SNARK-friendly hash such as Poseidon. JSON is not acceptable because its canonicalization is underspecified.

The scope grammar should be designed up front and treated as a stable interface. Adding scope dimensions later is expensive because it requires circuit changes and redeployment. A typical minimal scope grammar includes action type (an enumerated value), resource identifier (a field element or hash), an optional amount cap (a field element), and a jurisdiction or realm identifier (an enumerated value). More elaborate scopes are possible but increase circuit size roughly linearly per added dimension.

### 4.3 Application Container

The application container is the untrusted zone where most of the system's logic lives. It generates an ephemeral keypair `(sk_session, pk_session)` at the start of each proving session, using a cryptographically secure RNG. It authenticates to Vault using a normal Vault auth method (AppRole, Kubernetes ServiceAccount, JWT, etc.) and requests a delegation by sending `pk_session` and a scope specification. It receives the signed delegation back.

The application then invokes the proving library with the delegation signature, the session private key, the scope parameters, and the public statement as the circuit's private and public inputs respectively. The prover runs in this container's memory. When proving completes, the application transmits only the resulting proof and public inputs to the verifier. The session private key and the delegation signature are zeroized.

It is important to understand what this container is and is not trusted with. It *is* trusted with the delegation credential for the credential's lifetime, and a compromise during that window allows the attacker to generate proofs within the delegated scope. It is *not* trusted with anything beyond that scope, and it is *not* trusted to protect the root key, because it never holds the root key. The security model tolerates application compromise; it does not tolerate prolonged undetected application compromise plus a negligent scope grant.

### 4.4 Proving Circuit

The circuit has two parts: a signature verification subcircuit and a statement subcircuit.

The signature verification subcircuit takes as private inputs the delegation signature `sig`, the signed message (reconstructed from `pk_session`, scope, expiry, nonce), and the session private key `sk_session`. It takes as public inputs the root public key `pk_root` and the current time. It computes, in-circuit, the canonical encoding of the message, verifies that `sig` is a valid signature of that message under `pk_root`, verifies that `pk_session` corresponds to the private `sk_session`, and verifies that the expiry is in the future relative to the public current time.

The statement subcircuit takes the scope parameters as private inputs (or public, depending on the privacy requirements) and the statement's public inputs as public. It evaluates whatever predicate the application is proving — that a transaction is within the delegated amount cap, that a claimed attribute matches an authorized resource identifier, that an action type is in the permitted set.

The two subcircuits are wired together by constraining the statement's authorization context to equal the scope that was signed. An unscoped or out-of-scope statement cannot produce a satisfying witness.

### 4.5 Verifier

The verifier holds only public parameters: the verification key for the proof system, the root public key `pk_root`, and whatever public statement is being attested. The verifier does not interact with Vault, does not need to be online, and does not need any private state. Verification is a single, deterministic, typically millisecond-scale operation.

A successful verification establishes that some party in possession of a valid Vault-issued delegation generated this proof, and that the statement is true under that delegation's scope. It does not establish *which* delegation, which caller, or any other facts beyond the public inputs. If the application's audit requirements demand linking proofs to callers, this must be done through an external channel (a signed submission envelope, an authenticated transport), not through the proof itself, because the proof is specifically designed to hide this information.

---

## 5. Data Flow

The end-to-end flow for a single proof proceeds as follows. The application container generates `(sk_session, pk_session)`. It authenticates to Vault and submits a delegation request containing `pk_session` and the desired scope. Vault's policy layer validates the caller's entitlement to the requested scope, applies rate limiting, constructs the canonical delegation message with a freshly chosen expiry and nonce, signs it with `sk_root`, writes an audit record, and returns the signature. The application assembles the proving witness from the signature, the session keypair, the scope, and the statement inputs. It runs the prover, producing a proof and a set of public inputs. It transmits the proof and public inputs to the verifier and zeroizes the witness. The verifier checks the proof against `pk_root` and the public inputs, accepting or rejecting.

In steady state, the application should batch delegation requests where possible, since each Vault round trip has nonzero latency and each proof currently takes seconds to minutes to generate depending on circuit size. Reusing a delegation across multiple proofs within its TTL is permitted and recommended, subject to the nonce handling rules of the chosen signature scheme.

---

## 6. Security Analysis

### 6.1 Threat Model

The threat model assumes the following adversary capabilities. Full compromise of the application container, including arbitrary read and write of process memory. Full compromise of the container orchestrator and the host OS on which the application runs. Network observation and manipulation between application, Vault, and verifier, subject to standard TLS protections. Compromise of any number of past or concurrent prover instances. The adversary cannot compromise Vault itself; this is the root trust assumption and is enforced by Vault's own operational practices (unsealing procedures, storage backend access controls, auth method configuration).

### 6.2 Properties Preserved Under Adversary

Under this adversary, the root key `sk_root` remains confidential because it never exists outside Vault. The ability to generate new delegations remains exclusively within Vault, because delegation issuance is gated by Vault's auth and policy layers. Forged delegations are infeasible because they require forging `sk_root`'s signature. Out-of-scope proofs are infeasible because the circuit constrains the statement to the signed scope. Expired-credential abuse is infeasible because the circuit checks the signed expiry against the public current time. The adversary's achievable goal is limited to generating valid proofs within the scope of delegations they can induce a compromised application to request, during the TTL of those delegations.

### 6.3 Residual Risks and Mitigations

The scope grammar is a load-bearing security boundary. If it is too coarse — for instance, a scope that says merely "spending" without an amount cap — a compromised application can cause significant damage within that scope. Scope grammars should be designed to partition authority as finely as circuit cost allows.

Delegation TTLs trade off security against availability. Very short TTLs minimize the compromise window but increase Vault load and coupling between the application and Vault's availability. A starting point of five to fifteen minutes is typical; longer TTLs require correspondingly narrower scopes to remain safe.

The canonical encoding of the delegation message must be rigorously specified. Encoding ambiguities are a classic source of signature forgery vulnerabilities. The encoding logic should live in a shared library used by both Vault (or the wrapper that prepares messages for Vault) and the circuit, to guarantee they agree byte-for-byte.

The current time in the circuit is a public input and must be sourced from a trusted time authority at verification. If the verifier trusts the prover's claimed current time, the expiry check is meaningless. On-chain verifiers can use block timestamps; off-chain verifiers should use signed time from a trusted source.

---

## 7. Comparison with Alternative Architectures

### 7.1 TEE-Based Remote Prover

In the TEE approach, Vault releases the plaintext witness to an attested enclave that performs proving inside hardware-isolated memory. The trust boundary is extended to include the enclave.

The TEE approach has the advantage of not requiring any change to the circuit or the semantics of what is proved; any existing proof-of-knowledge-of-root-key circuit works unmodified. It has the disadvantage of adding a hardware trust assumption that is meaningfully weaker than pure cryptography, of requiring specialized infrastructure (SGX, TDX, SEV-SNP, Nitro) that is not uniformly available, and of complicating audit because Vault cannot see what the enclave does with the released witness beyond "it was released to an attested binary."

The TEE approach is appropriate when the statement genuinely requires root key material inside the circuit — a rare case in practice — or when restructuring the proving semantics is operationally infeasible.

### 7.2 MPC-Based Collaborative Proving

In the MPC approach, the root witness is secret-shared across multiple non-colluding parties, and the proof is generated jointly using a collaborative proof system such as coSNARK or DIZK. No single party ever reconstructs the witness.

The MPC approach has the advantage of eliminating all single points of trust, including hardware. It has the disadvantage of very high overhead — collaborative proving is typically 10 to 100 times slower than single-party proving, with network round trips between parties adding further latency — which restricts it to high-value, low-frequency operations such as treasury signings or key ceremonies.

### 7.3 Positioning

The delegation architecture described in this document is the recommended default for per-request production workloads. TEE-based proving is the appropriate fallback when the statement resists restructuring. MPC proving is reserved for root-of-trust operations where the cost of any single-party trust is unacceptable. Most mature production systems combine all three: delegation for the hot path, enclaves for cases where scope grammars cannot express the required authority, and MPC for the key ceremonies that establish `sk_root` itself.

---

## 8. Operational Considerations

Rolling `sk_root` is a planned event that must be coordinated across all verifiers, since changing `pk_root` invalidates all past verification. A key-rotation protocol that publishes the new `pk_root` alongside a signed transition statement from the old `pk_root` allows verifiers to update without interruption.

Circuit upgrades are also planned events. Any change to the scope grammar, the canonical encoding, or the signature verification logic requires a new verification key, which must be distributed to verifiers before proofs against the new circuit can be accepted. Running old and new circuits in parallel during migration is standard practice.

Monitoring should include Vault delegation issuance rates per caller, delegation TTL distribution, proof generation latency, verifier rejection rates, and the age of the oldest in-flight delegation. Spikes in any of these are early indicators of operational problems or active compromise attempts.

Disaster recovery for Vault follows standard Vault practice. The additional consideration is that if Vault is offline, no new delegations can be issued, but existing unexpired delegations can still be used to generate new proofs. This is usually desirable availability behavior but should be explicitly understood by the operations team.

---

## 9. Summary

The paradox that motivates this architecture dissolves once the circuit is restructured to prove knowledge of a delegation rather than knowledge of the root key. Vault retains sole custody of root material, which matches its purpose. The circuit receives a witness, which matches its purpose. The bridge is a short-lived, narrow-scope signature whose compromise is operationally containable. The resulting system admits a small, well-defined trust boundary, a clean audit model, and verification semantics that require no trust in the prover's environment.

This is why, despite the implementation work involved in standing up a ZK-friendly signing mount and designing a scope grammar, signature-delegation proving has become the dominant pattern for production ZK systems integrated with enterprise secret management.
