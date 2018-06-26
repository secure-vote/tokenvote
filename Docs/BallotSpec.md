# Ballot Specification

## V2

```
{
  "ballotVersion": 2,
  "ballotInner": {
    "ballotTitle": "Title",
    "shortDesc": "short",
    "longDesc": "long",
    "subgroup": null,
    "discussionLink": null,
    "encryptionPK": null
  },
  "optionsVersion": 3,
  "optionsInner": {
    "options": null,
    "aux": null
  }
}
```

## Changelog

### v1 -> v2

- [x] start and end times no longer included in ballot spec - stored in smart contract instead (avoid's ambiguiety)
- [x] remove binding prop
- [x] subgroup is `null` for the moment, will be populated later
- [x] options moved to root from inside ballotInner (only valid on ballotVersion >= 2)
- [x] `aux` added to optionsInner that will later house props like `quorum` or other specific requirements

## Index v2

Interface: [sv-light-smart-contracts/svLight/contracts/IndexInterface.sol](https://github.com/secure-vote/sv-light-smart-contracts/svLight/contracts/IndexInterface.sol).

When calling `initDemoc` a new admin contract will be created. 
This admin contract is used for creating new ballots, creating community ballots, managing admin features, and paying for the democ on TokenVote.
`initDemoc` also returns the democHash, which is also emitted in a `DemocAdded` event along with the admin address.

Call `getDAdmin` to get the admin address.

Index v2 and beyond uses a modular backend and upgrade system to ensure continuity of data without compromising our ability to improve the ix.

The Ix tracks democracies, and for each democ:

- ballots
- categories
- admin sc
- name of democ

### the `packed` param for ballots

Ballot SCs now take a `uint256 packed` parameter. It's a composition of several smaller parameters and has the following layout:

`[112 bits - unused][16 bits - submissionBits][64 bits - startTime][64 bits - end Time]`
