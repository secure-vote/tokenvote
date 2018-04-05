# The Democracy Index

There is a central smart contract that manages democracies and permissions / payments around them.

## Changes when updating to v1.2

* Introduce mechanism to deprecate / upgrade
  * Should have an `upgraded() returns bool` method to detect upgrades
  * Should have an `sucessor() returns address` method to point to new SC
* Should instantiate admin proxy SC by default
* Should be backwards compatible with earlier democracies (things like index count, lookups, etc)
