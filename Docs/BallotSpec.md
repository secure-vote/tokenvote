# Ballot Specification

## Changelog

### v1 -> v2

- [x] start and end times no longer included in ballot spec - stored in smart contract instead (avoid's ambiguiety)
- [x] new ballot box SC mode: unencrypted but includes pubkey (useful for signing, etc) - note, might be better to use 0x0 privkey for curve25519 and detect that way
  * ballot modes are now: unencrypted (using Eth addresses), encrypted (using Eth addresses), unencrypted using SV ID, encrypted using SV ID
- [x] Ix (index) has `function getVersion() external constant returns (uint256)` - returns 2 for version 2, errors on v1

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

## Ballot Spec v2

```
{

}
```
