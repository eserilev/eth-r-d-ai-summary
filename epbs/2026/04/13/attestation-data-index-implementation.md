# Attestation data.index mechanism removal discussion

**Channel:** epbs | **Date:** 2026-04-13

## Summary

nc1234 proposed removing the attestation data.index mechanism (which signals full vs empty payload status) if payload processing is delayed, arguing that a beacon block already indicates whether its parent is full or empty, making the [0, 1] index signaling potentially redundant.

potuz disagreed with this proposal, explaining that the mechanism is still necessary. When a committee member at slot N+1 rejects the block at N+1 and wants to vote for block N as head instead, they must explicitly signal whether they're voting for (N, full) or (N, empty), since the root of block N alone doesn't convey this full/empty distinction. This clarifies that the data.index mechanism serves a critical role that cannot be replaced by block root information alone.

## Participants

- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T20:54:05.525000+00:00] nc1234: I am just thinking if we can remove attestation's data.index mechanism if we delay processing payload. I think a beacon block already indicates whether its parent is full or empty, so attesting to it already express attester's view on payload availability. I am not sure if there will be any consequence if we get rid of data.index = [0, 1] thing. Should we preserve this signal? cc. <@755590043632140352> <@520034910149410861>
[2026-04-13T20:56:21.761000+00:00] potuz: Nah this is wrong. When you are in the committee of slot N+1 and you don't like the block of N+1, you want to vote for, say N as head. You *must* signal if you are voting for (N, full) or (N, empty) and the root of N does not tell you if it is full or empty
```

</details>
