# Optimistic sync testing requirements

**Channel:** epbs | **Date:** 2026-02-11

## Summary

potuz raised concerns about optimistic sync testing requirements for current Ethereum forks, highlighting a critical consensus rule that must be properly tested to prevent network splits. Specifically, when a payload is marked as INVALID, the consensus layer must continue building on top of the empty slot since the CL block itself remains valid, even though the execution payload is removed.

This behavior creates particular complexity for optimistic sync implementations and forkchoice logic, as nodes behave differently during optimistic syncing versus when processing invalid blocks received through gossip. potuz emphasized that this scenario is difficult to catch in testing due to these behavioral differences and has been a source of mistakes in code reviews, stressing the importance of comprehensive test coverage for this edge case.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-11T15:21:23.514000+00:00] potuz: <@592004585506340885> do you know which optimistic sync tests do we have for the current forks? The following *must* be tested or it will be a consensus split: but if the payload is INVALID, the chain **MUST** continue building on top of the  empty slot, the CL block is still valid. This situation of removing full nodes but not necessarily the empty ones is actually very tricky for optimistic sync and this was essentially all of the complication for me in forkchoice, so I want to stress this since this has come up also as a mistake in reviews. This is also a little hard to catch on testing cause nodes behave differently when syncing optimistically than when catching invalid blocks over gossip.
```

</details>
