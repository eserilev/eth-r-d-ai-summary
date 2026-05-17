# Attestation data.index mechanism and payload processing delay

---

## 2026-04-13 (epbs)

nc1234 proposed removing the attestation data.index mechanism if payload processing is delayed, arguing that beacon blocks already indicate whether their parent is full or empty, making the data.index = [0, 1] signal potentially redundant for expressing attester views on payload availability.

potuz disagreed with this proposal, explaining that the data.index mechanism is necessary for a specific scenario: when a committee member in slot N+1 doesn't approve of the N+1 block and wants to vote for slot N as head instead, they must be able to signal whether they're voting for (N, full) or (N, empty). The root of block N alone cannot distinguish between these two states, making the data.index signal essential for proper attestation semantics.

The discussion appears to conclude that the data.index mechanism should be preserved, though no explicit consensus or next steps were stated.

**Participants:** nc1234, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-13T20:54:05.525000+00:00] nc1234: I am just thinking if we can remove attestation's data.index mechanism if we delay processing payload. I think a beacon block already indicates whether its parent is full or empty, so attesting to it already express attester's view on payload availability. I am not sure if there will be any consequence if we get rid of data.index = [0, 1] thing. Should we preserve this signal? cc. <@755590043632140352> <@520034910149410861>
[2026-04-13T20:56:21.761000+00:00] potuz: Nah this is wrong. When you are in the committee of slot N+1 and you don't like the block of N+1, you want to vote for, say N as head. You *must* signal if you are voting for (N, full) or (N, empty) and the root of N does not tell you if it is full or empty
```

</details>

