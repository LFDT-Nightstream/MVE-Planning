# Deciding which MPC protocol we should prefer

## Query

if I want to settle the MPC protocols only a lattice-based zkVM, which
1. introduces the fewest new primitives
2. has the smallest computational overhead when combined together (i.e. compose well)

## Response
1. Arithmetic secret sharing + Beaver triples

This introduces the fewest new moving parts relative to Neo/SuperNeo’s native algebraic model.

What the zkVM needs to verify is mostly:
- share consistency,
- linear updates,
- multiplication correctness using preprocessed correlations,
- MAC/authentication checks if you use SPDZ-style active security.

Those are algebraic checks and fit CCS naturally. By contrast, MP-SPDZ’s documentation describes SPDZ as an offline/online protocol family where preprocessing generates correlated randomness and the online phase consumes it; that split is exactly the sort of thing you can settle cleanly as a sequence of algebraic invariants.

So on your two criteria:

Fewest new primitives:
- secret sharing,
- correlated multiplication material (triples),
- optionally MACs/authenticated shares.

That is much closer to Neo/SuperNeo than OT/VOLE machinery or garbled-circuit labels.

Smallest combined overhead:
- not necessarily the absolute fastest MPC in isolation,
- but likely the best composed cost inside Neo/SuperNeo, because transcript verification is algebraic and low-friction.

2. VOLE-based arithmetic MPC

VOLE-based ZK and MPC are attractive because the prover cost can be very low, and VOLE-in-the-Head is reported as “simpler, smaller and faster” than related MPC-in-the-Head approaches. But the VOLE literature also relies on extra technical infrastructure such as pseudorandom VOLE generation tied to LPN-style machinery and OT-based tools.

For a Neo/SuperNeo zkVM, that means you are adding a second primitive stack that is not especially native to the outer proof system.

So I would put VOLE here:
- better than OT/garbled circuits for arithmetic workloads,
- worse than Beaver/SPDZ-like arithmetic MPC on “fewest new primitives,”
- maybe competitive on wall-clock performance after serious engineering.

3. MPC-in-the-Head

This is where the answer flips compared with a generic lattice proof stack.

MPC-in-the-Head is attractive when it is your proof system. It has also been extended to Z_(2^k), and newer work like Diet is explicitly promising for LWE/Kyber/Frodo-style relations.

But inside Neo/SuperNeo, it is usually the wrong composition layer, because then you are proving:
- commitments to simulated MPC views,
- challenge openings,
- Fiat–Shamir logic,
- consistency checks for a proof protocol,

inside another proof protocol.

That duplicates transcript hashing, commitment logic, and proof bookkeeping. In a Neo/SuperNeo zkVM, that is usually less natural than directly proving the algebraic correctness of an arithmetic MPC transcript.

So MPC-in-the-Head is good for:
- standalone PQ ZK,
- lattice-native proof-of-knowledge relations,

but not my first choice for settling MPC on top of Neo/SuperNeo.

4. Avoid if possible: garbled circuits / OT-heavy 2PC

Even though Neo/SuperNeo have pay-per-bit commitment costs, that does not make bit-heavy GC/OT composition the best choice. Neo still fundamentally benefits from sparse algebraic structure over small fields, while garbled circuits introduce labels, table lookups, and many bit-level checks. OT/VOLE-based public-verifiable transformations also add their own technical tools.

5. Worst composition: HE-based MPC

Even though your zkVM is lattice-based, HE-based MPC is still usually the worst composed option.

You would be proving correctness of:
- ciphertext arithmetic,
- modulus switching / relinearization / NTT-heavy logic,
- much fatter state transitions.

That is generally much more expensive than proving secret-share algebra.
