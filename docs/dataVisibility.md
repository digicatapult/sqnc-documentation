# Data visibility in SQNC

As of writing this piece all metadata information is publicly available. We store information in metadata in the following ways:

- Data file contents are stored in plaintext in IPFS.
- Metadata values are stored on-chain in plaintext.
- Metadata keys are stored on-chain in plaintext.

In addition to this even if we moved all of this information into an encrypted form we would still leak further information about metadata through other channels:

- Process restrictions place restrictions on relationships between metadata values on different tokens.
- The existence of the token transactions leaks information about what tokens are related.

Fundamentally restricting this sort of information would likely require a rethink of the token model we’ve taken. Likely it would result in losing the guardrails of process flows entirely. This investigation therefore assumes that this is beyond scope and will simply focus on strategies for hiding the metadata and data themselves.

# Data File Contents

Files in IPFS are shared freely and no access control mechanisms are implemented therein. This is a limitation of IPFS and there are no technical reasons why a different content-addressed distributed filestore couldn’t provide these features. For now however we consider approaches to encrypting the files that are placed in IPFS.

Files placed in IPFS may be large. It therefore only makes sense to encrypt them with a symmetric block cipher. The challenge therefore is how to share the symmetric encryption key with other parties. This should clearly happen on-chain via an appropriate key exchange mechanism to prove that sharing occurred, but this presents an additional concern; what happens if a party gives two different symmetric keys to different chain members? The risk here is that the canonical contents of the file as described by the on-chain reference are undermined.

Essentially what we need is something called [publicly verifiable secret sharing](https://en.wikipedia.org/wiki/Publicly_Verifiable_Secret_Sharing). Under a publicly verifiable secret sharing scheme a party is able to privately share a secret (in this case a symmetric encryption key) with another party whilst proving to all other chain members that the secret has indeed been shared correctly. A commonly used method for this is the [Chaum-Pedersen Protocol](https://chaum.com/wp-content/uploads/2021/12/Wallet_Databases.pdf). Essentially the protocol proves that two encryptions correspond to the same underlying plaintext.

# Implementation Considerations:

1. This is somewhat complex cryptography that requires some knowledge of number theory to implement.

2. Chaum-Pederson is well implemented. One version that we can likely adapt is [electionguard-verifier/chaum_pedersen.rs at main · microsoft/electionguard-verifier](https://github.com/microsoft/electionguard-verifier/blob/main/src/crypto/chaum_pedersen.rs) which is held under an MIT license.

3. We likely don’t want to implement this on a Prime Field of Integers but likely instead want to use an EC-ElGamal scheme. This is to minimise the size of transactions associated with key sharing operations. We would likely implement this on the Ristretto group as found in [curve25519_dalek - Rust](https://doc.dalek.rs/curve25519_dalek).

4. When sharing the symmetric key we shouldn’t share the key directly (a 256bit key doesn’t even fit in the curve above). Instead we should secretly share a randomly generated curve point in the EC group as the message. The hash of this point can then be used as a symmetric key.

5. The proof has to be able to be evaluated in a Substrate runtime. We therefore need to make sure all code can run in a no_std environment.

Our aim should likely be to implement a toy version of this as part of a further ticket. The implementation of that can then be used as part of a more functional implementation.

# Metadata Values and Keys

Metadata values hold substantial information about the status and delivery timelines for a given part. These values also allow a malicious member to download all IPFS files in the hope they will be able to decrypt them later. Encryption of the actual IPFS address would prevent this. It is therefore useful to consider how we might hide this information.

Firstly, if we assume we don’t need restricted process flows, we can trivially encrypt the values as per the section above on Metadata File Contents. Complications only arise if we wish to enforce process restrictions as we cannot assert equality between an encrypted metadata key associated with a process restriction and an encrypted metadata key associated with a token.

Individual restrictions only perform equality checks on metadata values and lookups of keys (which can also be considered an equality check). Therefore if we can encrypt each key and value using a scheme similar to the metadata file contents we can again require a transaction submitter provide proofs (Chaum-Pederson) that the necessary constraints have been met. The essential difference is that instead of encrypting a symmetric key we encrypt an actual value. Note this does leak information that the constraints have passed but not the underlying plain-texts.

# Implementation Considerations

1. Most considerations from the data file contents section still apply

2. Encrypting metadata values directly (some of which might form a limited sized enum) will require a padding scheme such as [Optimal asymmetric encryption padding](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding). This will require further limiting metadata sizes to incorporate padding

3. Bloat on the transaction size is substantial

It’s worth noting this is of substantially lower value than encrypting data file contents. Information only needs to be held directly on tokens if we interact with that information in process flows. One could argue that it is desirable in this case to just not have the guardrails provided by process flows and place the information in file contents.
