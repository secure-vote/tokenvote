# Delegation (Note: out of date)

Delegation is managed via a smart contract - currently only delegation to Eth addresses are possible.

## Delegation v1.2 upgrade list

* Similar to `DemocIndex` we should have `upgraded() and successor()` methods
* Should be backwards compatible
* Should allow Eth addresses to delegate to arbitrary 32byte addresses - use a "multi-address" type format like a multihash - note, this will be 32 bytes so some truncation of 32byte public keys will occur - this can be done safely in the same way addresses are generated though.
* Allow anything to delegate to anything? Note: this could present some incompatibilities cross-network (so only some types of delegations should be allowed, e.g. Eth -> SvId but not SvId -> Eth)
