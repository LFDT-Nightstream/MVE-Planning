# zkLogin by Sui

**General idea of zkLogin:**
associate blockchain-friendly in-memory (ex: ed25519) keypairs to a master social login
1. Create an ephemeral in-memory keypair
2. Connect your social login to this keypair
3. Associate state with the social login (i.e. the social login is the master account, and you're just associating a blockchain-friendly keypair to it)
 
zkLogin allows having many ephemeral keypairs for the same social login (see [SyRA](./SyRA.md) for a variation that disallows this)

**General architecture of zkLogin:**
The goal is to cross-sign the ephemeral keypair and the social login (required for both to sign each other to link them)

Hard direction: social login -> keypair
1. OpenID Connect to Google. Their JWT system allows you to specify a "nonce", and we stuff into it the <ephemeral public key, expiration time>
2. Validate that the response compared to a list of known RSA keys from Google (https://www.googleapis.com/oauth2/v3/certs). This depends on these keys (which are occasionally rotated) to be known onchain
3. Generate a wallet based on (JWT subset + salt). This means different domains get different wallets (by design, to avoid one domain stealing funds from another). If you want the address to be consistent across websites, you have to use a wallet application (in which case, the "domain" is the wallet itself, so it's consistent across dApps). The salt ensures unlinkability (similar to blinding factor we use in nullifiers in Midnight)
4. Wrap all these steps in a ZK proof. This is ZK both from a succinctness point of view, but also from a privacy angle (to avoid having to share inputs, so nobody knows link between social login and address)

Easy direction: keypair -> social login
you get this automatically from the fact the address is (JWT subset + salt). You sign transactions including this address with your ephemeral key (which means you're implicitly signing the social login JWT with the ephemeral key)

**Managing the salt**
Since users need to keep track of the salt used (in the same way we need to track blinding factors for Midnight), Sui also has a solution to this called a *salt server*.
The salt server takes a user's JWT as input, and gives them back a deterministic salt to use for their JWT (calculated using some seed held in the salt server for the randomness).
To ensure this doesn't leak privacy, they run this in a TEE

**Session length**
The ephemeral keys are meant to expire eventually for safety reasons.

zkLogin sessions also expire eventually (since the upstream connection has an expiration). That comes from
- the fact that providers occasionally rotate their keys
- JWT (which Google Login gives as a response) expire
in practice though, zkLogin doesn't explicitly monitor after of these expirations (due to performance/feasibility). Rather, they just validate the zkLogin once and set their own custom expiry mechanism (ex: 30 days, then you have to re-validate). Both this credential *and* the ephemeral key are considered expired after this time.

**Requirements in the stack**:
- RSA verification in Nightstream
- Sha256 in Nightstream
- Groth16 verifier in Nightstream (or a post-quantum version of Groth16)
- User needs to backup their salt, so this depends on the same "restore dApp-specific secret data" open problem we have (for the same reason - storing blinding factors)