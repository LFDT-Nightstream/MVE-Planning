# P-256 Passkey → Nightstream Integration: Prototype Plan

## Problem Statement

The Midnight OS MVE requires biometric authentication (Face ID, Touch ID) for frictionless onboarding. Biometric auth on all major platforms (Apple, Android, WebAuthn) is locked to **P-256 ECDSA** at the hardware level. Nightstream operates over the **Goldilocks field** (64-bit prime), making P-256 (256-bit) non-native.

We need a path from passkey authentication to Nightstream-proved transactions.

## Proposed Architecture

### Two-phase design: authenticate once, sign cheaply forever

```
┌─────────────────────────────────────────────────────────┐
│                  ACCOUNT CREATION (once)                  │
│                                                           │
│  1. User taps "Sign up" → Face ID / Touch ID prompt      │
│  2. Secure Enclave generates P-256 keypair                │
│  3. Passkey signs a registration challenge                │
│  4. MPC generates Nightstream-native key (shares distributed)│
│  5. ZK proof links passkey pubkey → Nightstream pubkey       │
│     (P-256 verification happens HERE — expensive but once)│
│  6. Binding committed on-chain                            │
│                                                           │
│  Cost: ~15K-25K constraints (CCS) or ~500K-1M RISC-V     │
│  instructions (software path). Acceptable for one-time.   │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│               EVERY TRANSACTION (cheap)                   │
│                                                           │
│  1. User taps Face ID → passkey signs challenge           │
│  2. App verifies P-256 signature LOCALLY (not in ZK)      │
│  3. Successful auth unlocks MPC key shares                │
│  4. MPC produces Nightstream-native signature                │
│  5. Nightstream proves transaction with native signature  │
│                                                           │
│  Cost: native signature verification only (~few hundred   │
│  constraints). P-256 never enters the proof system.       │
└─────────────────────────────────────────────────────────┘
```

### Why this works

- **P-256 is an identity gate**, not a signing mechanism for every transaction
- The passkey proves "this is the real user" to the local app
- The MPC key proves "this transaction is authorized" to the chain
- The one-time binding proof establishes that the passkey holder controls the Nightstream key
- After that, Nightstream never touches P-256 again

---

## Passkey Binding

The passkey binding is the critical one-time operation that connects a user's hardware-bound P-256 passkey to their Nightstream-native key. This is what makes the entire architecture work — it's the bridge between the WebAuthn world (P-256) and Nightstream's proof system (Goldilocks).

The binding is **independent of the three implementation paths** below. It uses whichever path is available to perform one P-256 signature verification, then the result is committed on-chain. After that, Nightstream never needs to verify P-256 again.

### What the binding proves

A single statement:

> "The holder of P-256 passkey `PK_passkey` has authorized the creation of Nightstream key `PK_nightstream`, and both are controlled by the same user."

Concretely:
- **Public inputs:** `PK_passkey` (P-256 public key), `PK_nightstream` (Nightstream-native public key)
- **Private inputs:** `sig_passkey` (P-256 signature over a binding message)
- **Binding message:** `H("nightstream/bind" || PK_passkey || PK_nightstream || nonce)`
- **The proof verifies:** `ECDSA_P256_Verify(PK_passkey, binding_message, sig_passkey) == true`

### How the binding happens (account creation flow)

```
1. User taps "Sign up" → Face ID prompt
2. Secure Enclave generates P-256 keypair (PK_passkey, sk_passkey)
3. MPC protocol generates Nightstream-native keypair:
   - Shares distributed: device TEE, server, recovery guardian
   - PK_nightstream is derived from the shares (public, never secret)
4. App constructs binding message:
   binding_msg = H("nightstream/bind" || PK_passkey || PK_nightstream || nonce)
5. Secure Enclave signs the binding message:
   sig_passkey = ECDSA_P256_Sign(sk_passkey, binding_msg)
6. Nightstream proof is generated:
   - Public: PK_passkey, PK_nightstream
   - Private: sig_passkey
   - Proves: the passkey signed the binding message (P-256 verification)
7. Binding commitment stored on-chain:
   binding_record = {
     passkey_pubkey_hash: H(PK_passkey),
     nightstream_pubkey: PK_nightstream,
     binding_proof: proof,
     nonce: nonce
   }
```

### A note on PK_nightstream

`PK_nightstream` is a placeholder for whatever native signature scheme Midnight OS adopts. Nightstream itself is a proof system — it proves computations, but doesn't define how users sign transactions. The concrete key type is still TBD:

| Option | Key type | Tradeoff |
|---|---|---|
| Ed25519 (EdDSA/Schnorr) | 255-bit EC keypair | Mentioned in specs for DID derivation. Non-native to Goldilocks (~10K-15K constraints to verify) |
| secp256k1 (ECDSA) | 256-bit EC keypair | Ethereum-compatible. Non-native (~15K-25K constraints) |
| Goldilocks-native curve | ~128-bit EC keypair | Cheapest to verify (~hundreds of constraints). No standard curve exists yet |
| Lattice-based (Dilithium) | Lattice keypair | Post-quantum, native math. Large signatures (~2-3 KB), no ecosystem |

All options use public/private key pairs. The binding, the three implementation paths, and the MPC all work regardless of which is chosen — `PK_nightstream` just takes the concrete form of that choice.

**How PK_nightstream relates to the three paths:**

The three paths only handle the **P-256 side** of the binding — verifying the passkey signature. `PK_nightstream` appears in all three paths identically: as a **public input** to the binding proof that the P-256 signature commits to. The paths don't verify or process `PK_nightstream` itself — they just include it in the binding message so the proof attests "this passkey authorized this Nightstream key."

```
What the paths prove:
  ECDSA_P256_Verify(PK_passkey, H("nightstream/bind" || PK_passkey || PK_nightstream || nonce), sig)
                                                          ↑
                                               PK_nightstream sits here
                                               as data inside the message,
                                               NOT as something being verified

What verifies PK_nightstream:
  After binding, every transaction uses MPC to sign with sk_nightstream.
  The chain verifies that signature natively — this is cheap and
  independent of the P-256 paths.
```

In other words:
- **Paths 1/2/3** = how we prove the passkey signature is valid (expensive, one-time)
- **PK_nightstream verification** = how the chain checks transaction signatures (cheap, every time)
- The binding connects them: "whoever controls this passkey authorized this Nightstream key"

### Why it only happens once

After the binding is committed on-chain, the relationship `PK_passkey → PK_nightstream` is established. Every subsequent transaction:

1. The passkey authenticates the user **locally** (app verifies P-256 in JavaScript/native code, not in ZK)
2. Successful local auth unlocks the MPC shares (the device share is gated by biometrics)
3. MPC produces a Nightstream-native signature using only Goldilocks arithmetic
4. Nightstream verifies the native signature cheaply (cost depends on native key type chosen above)

The chain trusts the Nightstream-native signature because the binding proof already established that only the passkey holder could have created that Nightstream key.

### Re-binding (key rotation, new device)

If the user gets a new device or creates a new passkey:

```
1. User authenticates with existing MPC shares (recovery flow)
2. New passkey is created on the new device
3. A new binding proof is generated (same one-time P-256 verification)
4. Old binding is revoked, new binding committed on-chain
```

The MPC recovery shares enable this — you don't need the old passkey to create a new binding. The recovery guardians (server + backup) provide enough shares to authorize the re-binding.

### What gets stored where

| Data | Where | Visibility |
|---|---|---|
| P-256 private key (sk_passkey) | Device Secure Enclave only | Never leaves hardware |
| P-256 public key (PK_passkey) | On-chain (hashed) | Public hash, private preimage |
| Nightstream private key shares | Device TEE + server + guardian | Never combined |
| Nightstream public key (PK_nightstream) | On-chain | Public |
| Binding proof | On-chain | Public (proves the link) |
| Binding message + nonce | On-chain or derivable | Public |

### Cost of the binding (per path)

| Path | Binding cost | When it's paid |
|---|---|---|
| Path 1 (RISC-V) | ~8-16 min proving | Once at account creation |
| Path 2 (CCS gadget) | ~seconds proving | Once at account creation |
| Path 3 (precompile) | ~seconds proving | Once at account creation |

Even Path 1's 8-16 minutes is acceptable here — the user creates an account once. The app can show "Setting up your private account..." and process it in the background, or pre-compute the proof server-side and have the user verify it.

---

## Three Implementation Paths

All three paths achieve the same goal: verify a P-256 ECDSA signature inside Nightstream's Goldilocks-based proof system. They differ in engineering effort, performance, and developer experience.

---

### Path 1: RISC-V Software (no Nightstream changes)

**Idea:** Write a P-256 verifier in Rust, compile to RISC-V, run it inside Nightstream's VM. Nightstream proves each RISC-V instruction automatically. Zero changes to Nightstream.

**How it works:**
1. The P-256 verification algorithm (field arithmetic, point operations, ECDSA verify) is compiled to RISC-V machine code
2. Nightstream executes each RISC-V instruction and proves it via its lookup tables (Shout)
3. Every 256-bit field multiplication becomes ~50-100 native RISC-V instructions (shift, multiply, add, carry)
4. The full ECDSA verify becomes ~500K-1M RISC-V instructions total

**Performance:**
- Proving time: ~500-1,000 seconds (~8-16 minutes) at ~1 kHz
- Memory: should fit within 2GB, may need multi-fold chunking
- Proof size: standard Nightstream proof (~16 KB after compression)

**Pros:**
- Works TODAY with zero Nightstream modifications
- Uses existing `Rv64TraceWiring::from_elf(...)` path
- Good for prototyping and getting concrete measurements

**Cons:**
- Very slow — impractical for production
- Every RISC-V instruction (fetch, decode, execute) is proven individually, even though we only care about the math

---

### Path 2: CCS Constraint Gadget (custom circuit)

**Idea:** Express P-256 verification directly as CCS constraints over Goldilocks, bypassing the RISC-V VM entirely. The math is represented at the constraint level — no instruction fetch/decode overhead.

**How it works:**
1. Each P-256 field element is represented as 4 Goldilocks limbs (4 × 64 bits = 256 bits)
2. Field multiplication is expressed as constraints:
   - 16 limb-by-limb multiplications
   - Carry propagation constraints
   - Modular reduction (mod the P-256 prime)
   - Range checks (each limb fits in 64 bits)
3. Point addition/doubling is a composition of ~10-15 field multiplications
4. ECDSA verify (2 scalar multiplications + point comparison) is the top-level gadget

**Performance:**
- ~50-80 constraints per 256-bit field multiplication
- ~500-800 constraints per point addition
- ~15,000-25,000 constraints total for full ECDSA verify
- Proving time: seconds (folded into a single step)

**Pros:**
- ~100× faster than Path 1 (constraints encode math directly, no VM overhead)
- Production-viable performance
- Well-understood technique (Circom, Gnark, Halo2 all do this for non-native curves)

**Cons:**
- Requires writing a P-256 CCS gadget specifically for Nightstream's constraint system (`neo-ccs`)
- Must carefully handle:
  - Limb decomposition and range checks
  - Carry propagation (intermediate values can exceed 64 bits)
  - Modular reduction specific to the P-256 prime `2^256 - 2^224 + 2^192 + 2^96 - 1`
- Needs thorough testing and auditing — bugs in constraint gadgets can break soundness
- Exists outside the RISC-V VM, so Starstream programs can't call it directly without a bridge

**Implementation sketch:**
```
// Pseudo-constraint gadget structure
struct P256FieldElement {
    limbs: [GoldilocksVar; 4],  // 4 × 64-bit limbs
}

impl P256FieldElement {
    fn mul(&self, other: &Self, cs: &mut ConstraintSystem) -> Self {
        // 16 limb multiplications
        // carry propagation constraints
        // reduction mod p256_prime
        // range check output limbs
    }
}

struct P256Point {
    x: P256FieldElement,
    y: P256FieldElement,
}

impl P256Point {
    fn add(&self, other: &Self, cs: &mut ConstraintSystem) -> Self { ... }
    fn scalar_mul(&self, scalar: &P256FieldElement, cs: &mut ConstraintSystem) -> Self { ... }
}

fn ecdsa_verify(
    pubkey: &P256Point,
    msg_hash: &P256FieldElement,
    sig_r: &P256FieldElement,
    sig_s: &P256FieldElement,
    cs: &mut ConstraintSystem,
) -> BoolVar { ... }
```

---

### Path 3: RISC-V Precompile + Shout Lookup Tables

**Idea:** Add P-256 as a first-class precompile in Nightstream's RISC-V VM, just like the existing Poseidon2 precompile. Custom RISC-V instructions handle P-256 operations, backed by optimized Shout lookup tables.

**How it works:**
1. Define new RISC-V custom instructions (using CUSTOM-0 or CUSTOM-1 opcode space):
   - `p256_field_mul(rd, rs1, rs2)` — multiply two P-256 field elements
   - `p256_point_add(rd, rs1, rs2)` — add two P-256 curve points
   - `p256_scalar_mul(rd, rs1, rs2)` — scalar multiplication
   - `p256_verify(rd, rs1, rs2, rs3)` — full ECDSA verify (highest level)
2. Each precompile instruction is proven via dedicated Shout lookup tables
3. The lookup tables encode the correct input→output mapping for each operation
4. RISC-V programs call these instructions like any other instruction

**Performance:**
- Similar constraint count to Path 2 (~15K-25K constraints)
- But integrated seamlessly into RISC-V execution — no separate circuit needed
- Starstream programs can call P-256 operations naturally (compiled to these instructions)

**Pros:**
- Best developer experience — write Rust, call P-256 functions, it just works
- Same performance as Path 2
- Follows established pattern (Poseidon2 precompile already exists)
- Composable — can be used from any RISC-V program, not just dedicated circuits

**Cons:**
- Largest engineering effort of all three paths
- Requires designing lookup tables for P-256 operations (256-bit field arithmetic tables are much larger than Poseidon2 tables)
- The Shout table for 256-bit multiplication is enormous if not decomposed — needs sub-table decomposition similar to how Jolt handles large lookups
- Must integrate with Nightstream's existing CPU bus, trace wiring, and memory sidecar
- Changes the ISA surface — needs careful spec and testing

**Implementation scope (following Poseidon2 precompile pattern):**
```
Files to create/modify:
  neo-memory/src/riscv/lookups/isa.rs        — new P256 instruction variants
  neo-memory/src/riscv/lookups/encode.rs     — encoding for CUSTOM-0/1 P256 instructions
  neo-memory/src/riscv/lookups/cpu.rs        — P256PrecompileCtx state machine
  neo-memory/src/riscv/lookups/decode.rs     — decoding P256 instruction words
  neo-fold/src/memory_sidecar/memory/precompiles/p256/  — claim builders, terminal checks
  nightstream-sdk/src/p256.rs                — guest-side helper functions
  neo-memory/Cargo.toml                      — p256-precompile feature flag
```

---

## Path Comparison

| | Path 1: RISC-V Software | Path 2: CCS Gadget | Path 3: Precompile |
|---|---|---|---|
| **Nightstream changes** | None | New constraint gadget | New precompile + lookup tables |
| **P-256 verify cost** | ~500K-1M instructions | ~15K-25K constraints | ~15K-25K constraints |
| **Proving time** | ~8-16 minutes | ~seconds | ~seconds |
| **Engineering effort** | Days | Weeks-months | Months |
| **Developer experience** | Write Rust, compile to RV | Must use constraint API | Write Rust, call precompile |
| **Composability** | Any RISC-V program | Standalone circuit only | Any RISC-V program |
| **Production viable** | No (too slow) | Yes | Yes |
| **Good for prototype** | Yes | No (too much upfront work) | No |
| **Follows existing pattern** | Yes (existing ELF path) | Partially | Yes (Poseidon2 pattern) |

---

## Recommended Phased Approach

| Phase | Path | Purpose | Timeline |
|---|---|---|---|
| **Now (prototype)** | Path 1 | Prove feasibility, get measurements | Days |
| **V1 (medium-term)** | Path 2 | Production-viable P-256 in proofs | Weeks-months |
| **V2 (long-term)** | Path 3 | Seamless DX for all RISC-V programs | Months |

Each phase is a strict improvement. Path 1 validates the architecture. Path 2 makes it fast enough. Path 3 makes it easy to use.

---

## Prototype Implementation (Path 1 Detail)

### Goal

Prove that Nightstream can verify a P-256 ECDSA signature end-to-end, using only existing infrastructure. No changes to Nightstream. Slow but functional.

### Step 1: P-256 ECDSA verifier in Rust (no_std)

Write a minimal P-256 ECDSA signature verifier targeting RISC-V. Use existing crates where possible.

**Candidate crates:**
- `p256` (RustCrypto) — mature P-256 implementation, may need `no_std` adjustments
- `ecdsa` (RustCrypto) — generic ECDSA verification
- Alternatively, hand-roll minimal P-256 verify using `crypto-bigint` for field arithmetic

**The verifier function:**
```rust
#[no_std]
fn verify_p256_signature(
    public_key: &[u8; 64],   // uncompressed P-256 public key (x, y)
    message_hash: &[u8; 32], // SHA-256 hash of the message
    signature: &[u8; 64],    // (r, s) ECDSA signature
) -> bool {
    // 1. Decode r, s from signature
    // 2. Check r, s in [1, n-1]
    // 3. Compute u1 = hash * s^(-1) mod n
    // 4. Compute u2 = r * s^(-1) mod n
    // 5. Compute R = u1*G + u2*PubKey
    // 6. Check R.x == r mod n
}
```

**Deliverable:** A `no_std` Rust crate that compiles to RV64IM.

### Step 2: Compile to RISC-V ELF

```bash
# Target: riscv64imac-unknown-none-elf (strip down to rv64im)
cargo build --target riscv64imac-unknown-none-elf --release
```

Use the Nightstream SDK pattern from `nightstream-sdk` for guest programs. The ELF binary contains:
- Hardcoded test vectors (P-256 public key, message hash, signature)
- The verification function
- A main that runs verify and halts with success/failure

**Deliverable:** A RISC-V ELF binary that verifies one P-256 signature.

### Step 3: Prove with Nightstream

Use the existing `Rv64TraceWiring::from_elf(...)` path:

```rust
use neo_fold::Rv64TraceWiring;

let elf_bytes = include_bytes!("path/to/p256_verify.elf");
let wiring = Rv64TraceWiring::from_elf(elf_bytes)?;
let prepared = wiring.prepare()?;
let proof = prepared.prove()?;
let ok = proof.verify()?;
```

**Expected results:**
- Trace will be ~500K-1M RISC-V instructions
- Proving time: ~500-1000 seconds on a laptop (based on ~1 kHz rate)
- Memory: should fit within 2GB (single fold step, may need chunking)
- Proof verifies: yes/no — this validates the end-to-end path

**Deliverable:** A working proof that Nightstream verified a P-256 ECDSA signature.

### Step 4: Measure and document

Capture:
- Total RISC-V instruction count
- Proving wall time
- Peak memory usage
- Proof size
- Verification time

This gives the team concrete numbers for the Midnight West presentation.

### Step 5: Prototype the binding proof

Extend the prototype to prove the passkey binding:

```
Public inputs:  passkey_pubkey (P-256), nightstream_pubkey (native)
Private inputs: passkey_signature, binding_message
Proof:          "I know a valid P-256 signature from passkey_pubkey over
                 a message committing to nightstream_pubkey"
```

This is the one-time proof that binds a passkey to a Nightstream identity.

---

## What this prototype proves to the team

1. **Feasibility:** Nightstream CAN verify P-256 today, with zero changes
2. **Cost:** Concrete numbers for the performance overhead
3. **Architecture validation:** The two-phase design (expensive once, cheap forever) is sound
4. **Precompile justification:** The measured overhead justifies building a P-256 precompile or CCS gadget as a medium-term investment

---

## Relationship to MPC

This prototype is independent of the MPC protocol choice. Regardless of whether we use FROST, CGGMP, Shamir, or a hybrid:

- The passkey → Nightstream key binding proof is the same
- The per-transaction flow is the same (passkey authenticates locally, MPC signs natively)
- The MPC investigation can proceed in parallel

The MPC choice affects what "Nightstream-native signature" means. The passkey bridge just needs to link to whatever that is.

---

## Dependencies

- Nightstream `Rv64TraceWiring::from_elf(...)` path (exists, working)
- RISC-V RV64IM compilation target (exists)
- `nightstream-sdk` for guest programs (exists)
- P-256 Rust implementation targeting `no_std` + RISC-V (available via RustCrypto)
- No Nightstream modifications required

## Risks

- **Instruction count may exceed single-fold memory budget:** If the P-256 verifier generates >1M instructions, it may need multi-fold chunking. Nightstream supports this but it adds complexity.
- **RustCrypto `p256` crate may not compile cleanly to RV64IM:** May need to strip dependencies or hand-roll critical sections. The `crypto-bigint` crate is more likely to work in bare-metal `no_std`.
- **Proving time may be impractical for demo:** 8-16 minutes is long for a live demo. Consider pre-computing the proof and showing verification instead.
