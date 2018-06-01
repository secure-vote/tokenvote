# Data Structures in SV Light (TokenVote)

## Democracies

## Ballots and BBFarms

Ballots are maintained in a `BBFarm` or Ballot Box Farm.

Why "Farm"? Because using "backend" everywhere gets tiresome and uses more mental resources. 
The only "farm" we have is for ballot boxes, but we have an index db backend, a payments backend, etc.

### General

#### `ballotId`

a 256 bit integer with some special properties.

The last 224 bits (28 bytes) of the `ballotId` are reserved for the internal ballotId lookup within the BBFarm.
The first 32 bits (4 bytes) are used as a _namespace_ for the BBFarm. 
The namespace let's us know which BBFarm holds a ballot via a `ballotId` without doing any lookups (provided we 
know what the namespace means).

Using 224 bits for the internal ballotId means we can base it off a hash securely (useful for interacting with 
external data sources).

**For this reason it's important than any BBFarm uses a mask to zero out anything other than the
last 224 bits when doing lookups.**

When looking up ballots, do something like `ballots[ballotId & BALLOT_ID_MASK]` or `ballots[uint224(ballotId)]`.

### Submission

There are 4 params submitted when creating a ballot:

* `bytes32 democHash`
* `bytes32 specHash`
* `bytes32 extraData`
* `uint256 packed`

#### `specHash`

this is a 32 byte sha256 hash of the Ballot spec. 
by defualt, users should be able to request the spec over IPFS by prepending 
0x1220 (the multihash header for sha256) and using the `getBlock` api method.
(See `extraData` for exceptions).

#### `extraData`

This is a mostly unused parameter at the moment.

An early use we've identified is that the first byte will indicate the
particular `BBFarm` to use. This allows us to upgrade to new BBFarms over
time.

At least the first byte is dedicated to routing in the index (so the BBFarm 
doesn't need this info). My feeling at this stage is that something like the 
first 8 or 16 bytes should be dedicated to pre-BBFarm stuff (that the index 
will use). An advantage of this is we can zero them before passing to the
BBFarm which allows us to save some gas on storage.

Broadly, in 8 byte chunks the allocation of extradata is: `[index][bbfarm][ballotbox][ballotbox]`.

Specifically, it's broken down like:

```
[8 bits]   - the BBFarm to use (`bbFarmId` - note this is 
             particular to the index being used)
[56 bits]  - unallocated (for future Ix use)

[64 bits]  - unallocated (for BBFarm use)

[96 bits]  - unallocated (for future ballot use)
[32 bits]  - the IPFS header to use (if this is `bytes4(0)` 
             it indicates `0x00001220` (the sha256 mutlihash 
             header), and if the first 2 bytes are 0s it 
             indicates a CIDv0 where the header is only 2 bytes). 
```

Thus, the index takes a `bytes32 extraData` and passes a `bytes24` to the BBFarm, which passes a `bytes16` to the ballot itself.

#### `packed`

A uint256 that stores many relevant details.

It's structure is:

```
[112 bits] - currently unused
[16 bits]  - the `submissionBits` (flags describing 
             properties of the ballot, explained below)
[64 bits]  - the `startTime` of the ballot (seconds epoch)
[64 bits]  - the `endTime` of the ballot (seconds epoch)
```

#### `submissionBits`

There are many flags stored in `submissionBits`. 
Currently only a few flags have been allocated.
We use these as bit masks.

* `USE_ETH = 2^0` - true if the ballot uses Ethereum addresses
* `USE_SIGNED = 2^1` - true if the ballot uses ed25519 signed identities (unused currently)
* `USE_NO_ENC = 2^2` - true if the ballot is unencrypted
* `USE_ENC = 2^3` - true if the ballot uses curve25519 encryption
* (gap here)
* `IS_BINDING = 2^13` - true if the ballot is binding or not
* `IS_OFFICIAL = 2^14` - true if the ballot is official
* `USE_TESTING = 2^15` - true if ballot is in testing mode (note: the Index will require this is false on production ballots)

## Votes

### Using `submitVote(uint ballotId, bytes32 vote, bytes extra)`

// todo

### Using `submitProxyVote(bytes32[5] proxyReq, bytes extra)`

This method allows someone to submit a vote on a voter's behalf. A good usecase is fee-free voting, or voting in cases where voters don't have ETH themselves to pay for tx fees.

the `extra` param is used as in `submitVote`

`proxyReq` is broken down as below:

```
proxyReq[0] - the `r` param of the signature
proxyReq[1] - the `s` param of the signature
proxyReq[2] - packed data, see below
proxyReq[3] - the ballotId (as bytes32)
proxyReq[4] - the vote (as in `submitVote`)

proxyReq[2] is packed data as below:
  [8 bits] - the `v` param for ecrecover (first byte)
  [216 bits] - unallocated - expected 0s
  [32 bits] - a sequence number (last 4 bytes)
```
