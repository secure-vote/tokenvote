# BinaryVote spec

Ballots should be 32 bytes long and exactly one of:

* `yes` - `8000000000000000000000000000000000000000000000000000000000000000`
* `no` - `4000000000000000000000000000000000000000000000000000000000000000`

These are the equivelant for `RangeVotingPlusMinus3` with votes of `+1` or `-1` and one option.
