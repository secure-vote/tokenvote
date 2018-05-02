# Integration with SecureVote Light

SecureVote Light is a suite of software and smart contracts that will record which users are allowed to vote,
record individual ballots from users, and download and audit both the voter roll and votes themselves.

SecureVote Light uses a framework operating over the Ethereum network. 
Through the use of ed25519 signatures we're able to facilitate voting from offchain identities.
(In the technical docs, these are refered to as "Signed" ballots, instead of "Eth" ballots.)

The onboarding process for a user is simple: once they generate their voting keypair they provide the pk
to an oracle, which is responsible for ensuring the voting public key is recorded on-chain.
This oracle is a composite of two services: one to authenticate users to an outside voting platform, and
one (operated by SecureVote or the counterparty) to publish the user's voting details to the correct
on-chain location.

Rudimentary anonymisation is planned in an upcoming release.

Interactions with blockchain resources can be performed via the SV libraries (in dev)

## Actions required for integration - accounts

1. Users generates a key-pair through the counterparty's voting UI (ed25519).
2. Users authenticate to the counterparty and provide the public key.
3. The counterparty sends this public key to SecureVote (or directly to the blockchain) to ensure it's recorded in 
   the list of valid voters. [*todo: create lambda function to auth and accept these requests*]
4. The user monitors this and ensures their public key is accepted.

## Voting

1. Users browse to the counterparty's voting UI and are presented with a list of ballots (note: this can be
   retrived using SecureVote libraries - currently unreleased and in dev)
2. Users select a ballot (which is downloaded and cryptographically verified as correct) and are presented with
   the actual voting UI.
3. Users fill out their vote, and using SV libs this vote is packaged up into binary format.
4. Users sign their vote and pass it to SecureVote
5. SecureVote validates the ballot and signature and publishes it on-chain, providng the user with a txid
6. The user checks the transaction the ensure the data in the tx matches that which they provided and awaits
   confirmation of the transaction, at which point voting is completed.

Note: encryption of ballots is optionally supported (using curve25519), in which case their ed25519 public key 
is converted to a curve25519 pk for encryption purposes. The nonce should be set to `sha256.hmac(ballotHash, publicKey)` 
(with the curve25519 pk). An example library supporting this is [fast-sha256-js](https://github.com/dchest/fast-sha256-js).

If encryption is required then the UI needs to pull down the pk from the voting smart contract.

## Auditing

1. The voting UI (or whatever) passes details of the ballot to the SV auditing library
2. The auditing library downloads all ballots, the voter roll, and ancillary information
3. The auditing library validates all ballots (discarding invalid ballots) and then tallies results
4. Results and status messages are passed back to the voting UI via a function (provided when the UI calls the 
   auditing library). These messages conform to a known schema and show logs, balances (or vote weightings),
   delegations, and a single success or fail message.
   
It's then up to the UI to show these status messages and format results.
