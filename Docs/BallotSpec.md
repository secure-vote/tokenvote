# Ballot Specification

## Changelog

### v1 -> v2

* start and end times no longer included in ballot spec - stored in smart contract instead (avoid's ambiguiety)
* new ballot box mode: unencrypted but includes pubkey (useful for signing, etc) - note, might be better to use 0x0 privkey for curve25519 and detect that way
  * ballot modes are now: unencrypted (using Eth addresses), encrypted (using Eth addresses), unencrypted using SV ID, encrypted using SV ID
