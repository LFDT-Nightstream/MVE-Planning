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