# Fork Choice Test Enhancement with Proposal Step

**Channel:** epbs | **Date:** 2026-04-20

## Summary

The discussion centers around enhancing Fork Choice (FC) tests by adding a proposal step that would verify the `payloadAttributes` sent from Consensus Layer (CL) clients to Execution Layer (EL) clients. m.kalinin proposes using `get_proposer_head` to check the proposer head in each test case, making FC tests aware of the Engine API to catch inconsistencies between the proposal routine and fork choice logic. This enhancement could be integrated into compliance test generators and manual edge case testing.

tersec raises practical concerns about maintaining the ability to run tests with only a CL in isolation, noting that once tests become "real" network tests, dedicated orchestration tools like Hive or Kurtosis would be more appropriate. A specific compatibility issue is highlighted where Nimbus (and other CLs) cannot currently use Fork Choice Reorg (FCR) with Nethermind EL due to a known bug, which would force test implementations to either fake data, provide problematic data, or skip test portions entirely.

The conversation concludes with acknowledgment that while the Nethermind issue is an EL problem that should be caught by Hive testing, it creates practical challenges for implementing the proposed FC test enhancements. The discussion leaves open questions about the best approach to balance test isolation with real-world compatibility validation.

## Participants

- m.kalinin
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-20T03:54:24.488000+00:00] m.kalinin: What if we include proposal step into FC tests? With this the `payloadAttributes` a client sends to EL can be checked. `get_proposer_head` can be used to get the head that the proposal would use in each case. This would make FC aware of Engine API, but it's probably fine. Then we could add this step to our compliance test generator and create manual tests checking particular edge cases.

It's not necessarily should be a real proposal, but rather a check on the payload attributes and proposer head.
[2026-04-20T04:08:35.891000+00:00] tersec: as long as the tests can still be run with only a CL, in isolation
[2026-04-20T04:09:07.283000+00:00] tersec: i.e. once it becomes a "real" network test might as well use mechanisms designed to orchestrate that better (Hive, Kurtosis, etc)
[2026-04-20T04:13:01.363000+00:00] m.kalinin: I see the problem Potuz described as the following: proposal routine isn't coherent with the fork choice and calls EL with inconsistent data. Doing a basic check on the data doesn't require a real EL here
[2026-04-20T04:15:22.474000+00:00] tersec: yes, agree
[2026-04-20T04:18:20.326000+00:00] tersec: one general note is that right now, Nimbus (nor any other CL) actually can't use FCR with Nethermind: https://github.com/NethermindEth/nethermind/issues/11185
[2026-04-20T04:18:34.647000+00:00] m.kalinin: > I feel that creating scenarios and actually implementing them in client images to run in Kurtosis quickly blows up in complexity. 

This is one of the major problems with the FC tests as we have a very large variated of system states. Devnets help to address, but i don't see any better way than fuzzing or model based (any other alternative) + fuzzing approach to tackle this problem. At least now
[2026-04-20T04:19:02.807000+00:00] tersec: i.e. to some extent we'd fake things to pass any such EL-data test
[2026-04-20T04:19:10.308000+00:00] tersec: because there's no other choice
[2026-04-20T04:19:12.735000+00:00] m.kalinin: This should have a Hive test cc <@892053833121923094>
[2026-04-20T04:19:39.812000+00:00] tersec: and/or just skip those parts of the tests (more likely I guess?)
[2026-04-20T04:38:12.606000+00:00] m.kalinin: but the NM is an EL problem, not a CL one. And if there were a Hive test for this specific we would caught it much earlier
[2026-04-20T04:39:56.885000+00:00] tersec: doesn't matter we can't ship it
[2026-04-20T04:40:22.817000+00:00] tersec: and for test purposes, see above
[2026-04-20T04:42:36.016000+00:00] m.kalinin: I am not sure I get your point. Are you saying that we need a test env with real EL regardless?
[2026-04-20T04:45:40.068000+00:00] tersec: no
[2026-04-20T04:46:28.302000+00:00] tersec: I'm saying that the way Nimbus would have to test this is to one of (a) not provide the test the data it would provide to the EL; (b) intentionally provide ELs data which gets Nethermind wedged; or (c) skip that part of the test
[2026-04-20T04:46:40.499000+00:00] tersec: i.e. it wouldn't test what the CL does its usual flow
[2026-04-20T04:52:16.178000+00:00] m.kalinin: I see
```

</details>
