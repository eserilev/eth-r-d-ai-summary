# Checkpoint sync and state handling in ePBS

**Channel:** epbs | **Date:** 2026-02-20

## Summary

The discussion centers on checkpoint sync behavior in ePBS (enshrined Proposer-Builder Separation), specifically addressing how to handle state synchronization and whether there should be differentiation between block states and execution payload states. **potuz** strongly emphasizes that checkpoint sync should always receive the block state without ambiguity, and that clients should subsequently request payloads and blocks by range starting from the checkpoint slot to import them accordingly.

**potuz** identifies a significant complexity when slot 0 is missed, noting that attestations justifying the checkpoint signal whether slots are full or empty, but the current implementation returns "pending" as the justified checkpoint status rather than leveraging the full payload status information that could avoid reorgs. The team chose to maintain checkpoint signatures as block roots for simplicity, which **potuz** considers the right decision despite the trade-offs.

The conversation concludes with **potuz** acknowledging that justification processing knows whether full or empty branches are being justified (even when slot zero isn't missed), but implementing this knowledge would significantly complicate justification and finalization processing. The consensus appears to favor the simpler approach of always using block state for checkpoint sync and handling payload application through subsequent range requests.

## Participants

- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-20T08:19:43.689000+00:00] nc1234: To piggyback on this, if I receive a checkpoint state, would it be useful if there's a convenient way to tell whether this state is a block state or execution payload state (and thus knowing the view of the state provider on whether that slot was empty or full)?
[2026-02-20T09:00:48.520000+00:00] potuz: It has to be the block state!
[2026-02-20T09:01:14.011000+00:00] potuz: and you have to request anyway the payload by range and block by range starting from that slot, and import accordingly
[2026-02-20T09:01:56.912000+00:00] potuz: there should definitely not be any ambiguity about what state one should receive
[2026-02-20T09:39:49.415000+00:00] potuz: The biggest problem I see with checkpoints is when the slot 0 is missed, in those cases, attestations that justify the checkpoint actually are signalling for full or empty and we still return pending as the justified checkpoint when we *could* be returning the full payload status and avoid reorging the full justified payload. We chose to not change the signature of checkpoints to continue being the block root for simplicity and I think it was the right decision. I am unsure what we should be doing for checkpoint sync, but it seems to me that the simplest would be to always use the block state and just request all payloads and blocks by range starting with the state slot, and check if first the payload needs to be applied or not
[2026-02-20T10:06:22.487000+00:00] potuz: Well, the above is mostly stupid. Even when slot zero is not missed. Justification knows if the full or empty branch is being justified, but that would have also made justification and finalization processing more complicated
```

</details>
