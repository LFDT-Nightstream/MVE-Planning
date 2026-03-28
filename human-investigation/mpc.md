# MPC (Multi-Party Computation)

Multiparty Computation (MPC) is a cryptographic technique enabling multiple parties to jointly compute a function over their private inputs while keeping those inputs secret


## How MPC fits in

There are two main ways we can combine MPC with systems powered by Nightstream:
1. Running Nightstream itself as a coSNARK
2. Supporting general offchain MPC that settlement into Nightstream

The coSNARK approach is tricky as:
1. Nightstream is opcode-agnostic. That means that a system to target either a ZK or MPC backend for a high-level language requires repeating the work for each different language connecting to Nightstream (ex: one for Starstream, one for Comapact) 
2. Nightstream is ledger-agnostic. That means that a ledger-aware coSNARK system (i.e. where the witness contains ledger concepts) would require a different version for each ledger powered by Nightstream.

Therefore, focusing on facilitating general offchain MPCs that settle into Nightstream is the most deployment-agnostic path

## Performance considerations

There are many different ways to build MPC protocols, some which combine with Nightstream better than others.

Requirements:
- system should be "generic"
- works "efficiently" with Nightstream
- introduces the fewest new security assumptions

Note: we distinguish between threshold signature schemes (TSS) and general-purpose MPC frameworks, since they solve distinct problems.

### TSS approaches to investigate

The following do not introduce any new cryptographic assumption
- MuSig-L: Lattice-Based Multi-Signature With Single-Round Online Phase ([paper](https://eprint.iacr.org/2022/1036))
- Two-Round Threshold Signature from Algebraic One-More Learning with Errors  ([paper](https://eprint.iacr.org/2024/496))

### Generic MPC approaches to investigate

Note there is no one monolithic answer that fits within the confines of the requirements because of the way MPC-people define security based on the adversarial capabilities, corruption threshold, and output guarantees. Therefore,
- we limit recommendations to protocols that support arbitrary `n`, since there are many many many bespoke (2-4)-party solutions
- we omit any protocol with semi-honest security, since it is likely inadequately secure for any "real" deployment scenario

Given this, we recommend two where we can achieve the same or better security assumptions than that of Nighstream:
- Lowest overhead, weakest security (honest majority `t<n/2`, abort), no "new" hardness assumptions: Malicious Shamir secret sharing.
    - With an honest supermajority (t<n/3), you can achieve perfect/information-theoretic security (best possible)
- Highest overhead, strongest security (dishonest majority `t<n`, abort), potentially new/different hardness:
    - MASCOT - based on original SPDZ (and, thus, FHE and lattice hardness assumptions), lower offline/communication cost.
    - SPDZ2k - computation over rings (better compatibility with native CPU instructions), higher offline/communication cost, relies on oblivious transfer (OT), secure in the random oracle model.

In terms of an eventual implementation, the likely best option is to use MP-SPDZ. The beauty of [MP-SPDZ](https://github.com/data61/MP-SPDZ) is that, for a fixed program, you can choose from a host of different MPC protocols (implemented as "virtual machines") depending on the desired security properties, number of parties, output guarantees, etc. *without needing to modify the program itself*. You would instead simply compile the program using a different VM. Downside is it's not written in rust 🦀 
