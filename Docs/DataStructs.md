# Data Structures in SV Light (TokenVote)

## Democracies

## Ballots

Ballots are maintained in the `BBFarm` or Ballot Box Farm.

Why "Farm"? Because using "backend" everywhere gets tiresome and uses more mental resources. 
The only "farm" we have is for ballot boxes, but we have an index db backend, a payments backend, etc.

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

```
[8 bits]   - the BBFarm to use (`bbFarmId` - note this is 
             particular to the index being used)
[56 bits]  - unallocated (for future Ix use)
[64 bits]  - unallocated
[112 bits] - unallocated (for future ballot use)
[32 bits]  - the IPFS header to use (if this is `bytes4(0)` 
             it indicates `0x00001220` (the sha256 mutlihash 
             header), and if the first 2 bytes are 0s it 
             indicates a CIDv0 where the header is only 2 bytes). 
```

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
* `ALLOW_ETH_PROXY = 2^4` - (unimplemented) true if the ballot uses proxy submissions and ecrecover (can be used in parallel with `USE_ETH`)
* (gap here)
* `IS_BINDING = 2^13` - true if the ballot is binding or not
* `IS_OFFICIAL = 2^14` - true if the ballot is official
* `USE_TESTING = 2^15` - true if ballot is in testing mode (note: the Index will require this is false on production ballots)

## Votes
