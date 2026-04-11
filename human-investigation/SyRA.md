# SyRA: Sybil-Resilient Anonymous Signatures with Applications to Decentralized Identity

SyRA gives a *sybil resilient* version of [zkLogin](./zkLogin.md). This enables, for example, doing an airdrop where each Google email gets exactly one drop to their associated wallet.

- [paper](https://eprint.iacr.org/2024/379)
- [implementation](https://github.com/docknetwork/crypto/tree/main/syra)

The idea of SyRA is to essentially introduce a federated committee as an intermediate layer into the flow. Any number of OAuth checks by the user all deterministically map to a single "certificate" (certificate in the loose sense of a cryptographic key that is provably unique to your identity under the committee's mapping), and that certificate acts as your proof of personhood (facilitates sybil-resistance). 

The flow is as follows:
1. Federated members get together to create a joint public key for the committee (each member with their own private share)
1. User picks their unique identifier (ex: google account number, passport number, etc.)
1. User goes through an auth flow to prove their unique identifier is real and belongs to them (ex: Google OAuth process)
1. User splits the identity into shares, and commits to the shares
1. User generates a ZKP of
    1. Google OAuth flow correctness
    1. Share really come from the identity
    1. Commitment is of the shares
1. User broadcasts to each committee member
    1. one share of the identity (i.e. committee members can't know identity without collusion)
    1. commitment info for all the shares
    1. the ZK proof (so they know the identity belongs to you)
1. Committee generates a private key comprised of shares, that the user constructs into a new secret key (note: if t-of-n committee members are malicious, they can collude to reconstruct the user's key themselves, and deanonymize the system). This key essentially is the backing of your certificate of personhood under this committee. In this way, the committee is like an identity issuer.
1. When the user signs for a website, they sign using a their secret key (step 4) and a app-specific context (ex: "airdrop-2026") to create a Pseudonym
    1. The signature from step (5) comes with a ZK that the key was signed with the right context
1. dApp verifies the ZKP, and signature using the federation's public key (which ensures the key was derived via the SyRA process). Note: websites don't need their own keys (they just need a public agreed-upon context)

A few notes on how this differs from zkLogin by Sui:
1. There is no ephemeral key generated ahead-of-time that is connected to their Google account. Rather, the process is deterministic, and the same identity generates the same secret key for the user
1. zkLogin is specific to Google, but SyRA abstracts these details (and just assumes there is *some* way to verify some identity (be it a Google account ID with OAuth, or something else)). That means that, the ZK proof system used depends on this underlying system (i.e. can still use ideas in the Groth16 proof of Google OAuth from Sui, a zkTLS approach, etc.)
1. No salt server is required (as the process is deterministic), but a federated committee (stateless) is required instead
1. If a website doesn't want linkability, they can generate a unique context for every sign request (although now you need to prove it's unique every time, but this isn't hard by using things like timestamps in the context)

Note: picking the parameter committees (<t,n> in t-of-n) is up to the implementation, and have to be picked to give sufficient confidence of the safety to users. One of the easiest way to achieve this is to get the original identity provider in the committee (ex: Google as as committee member in the OAuth case), but this is tricky because if the original identity provider was to want to opt-into facilitating this scheme, they could instead just spend time refactoring their underlying system to give direct unique proofs of identity (instead of requiring the SyRA flow)

Note: this has both some similarities and differences with BBS+:
1. Both require per-issuer work to support pseudonym. However, the SyRA case just has one "issuer" (the committee)
1. BBS+ requires the issuer to keep track of issued credentials to avoid duplicates (stateful), whereas SyRA is deterministic (stateless)
1. SyRA allows you to sign for different contexts independently of the underlying authentication system (thanks to the indirection), whereas BBS+ uses the underlying authentication certificate every time.

**Requirements in the stack**:
- Post-quantum VRF (w/ specific properties required in the paper)
- Post-quantum semi-homomorphic commitment scheme
- Post-quantum semi-homomorphic encryption scheme
- Post-quantum DKG (Distributed Key Generation)
- Post-quantum Oblivious Transfer (OT) / Oblivious Linear Evaluation (OLE)
- Post-quantum version of any underlying authentication flow (ex: post-quantum equivalent to Groth16 for Google OAuth verification)
