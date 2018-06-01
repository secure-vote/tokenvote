## gas using in voting - 2018-06-01

    before removing voterLog
    Cost of casting a vote by proxy: 148494 gas
    Cost of casting a vote directly: 137950 gas
    Cost of casting a vote directly w 64 bytes of extra: 203200 gas

    after removing voterLog (but using mapping for votes + nVotesCast)
    // note: safe to do because we now have sequence numbers that can be used to check if someone has voted
    Cost of casting a vote by proxy: 123003 gas
    Cost of casting a vote directly: 97523 gas
    Cost of casting a vote directly w 64 bytes of extra: 162773 gas

    using Vote[] votes
    Cost of casting a vote by proxy: 123784 gas
    Cost of casting a vote directly: 98240 gas
    Cost of casting a vote directly w 64 bytes of extra: 163709 gas

    back to mapping
    Cost of casting a vote by proxy: 123067 gas
    Cost of casting a vote directly: 97523 gas
    Cost of casting a vote directly w 64 bytes of extra: 162773 gas

## gas for deploying a ballot (measured as deploying via index) - 2018-05-24 or there abouts

    base
    Deploy Ballot Gas Costs:
        Std:  1673103
        Lib:  1356171
        NoSC: 201314

    move unpack in svbackend to deeper function
    Deploy Ballot Gas Costs:
        Std:  1673093
        Lib:  1356161
        NoSC: 201176

    cut down Ballot struct in backend
        Std:  1627240
        Lib:  1310244
        NoSC: 155451

    remove (now) unused methods from svballotbox
        Std:  1392871
        Lib:  1310372
        NoSC: 155579

Note: eventually got this down much further using BBFarm

    /* bb farm won - by a lot
        Std:  1392871
        Lib:  1310372
        NoSC: 155579
        BBFarm: 274586
    */