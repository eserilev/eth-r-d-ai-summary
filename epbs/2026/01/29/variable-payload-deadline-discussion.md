# Free Option Problem and Deadline Parameters

**Channel:** epbs | **Date:** 2026-01-29

## Summary

The discussion centers on deadline parameters for an upcoming Ethereum fork and the associated free option problem. Potuz outlines the likely approach for setting two key deadlines: the first deadline will be minimized to allow maximum time for execution/proving after extensive testing, while the second deadline will need to accommodate realistic blob limits and gas limits for pure data blocks, but will be set as early as possible to minimize free option attacks.

There's uncertainty about whether penalties will be included in this fork due to complexity concerns and tight timelines, with a potential devnet targeted for February. Potuz suspects the fork may ship with known vulnerabilities to the free option problem, with penalties and other mitigations potentially deferred to a subsequent fork. The discussion also touches on how deadline timing relative to blob release versus payload release could influence whether attackers prefer using call data for free option strategies when it's cheaper than blobs.

The overall consensus appears to be that parameter decisions are still being determined through testing, with a balance needed between security considerations and practical implementation constraints given the development timeline.

## Participants

- anderselowsson
- christoph7800
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T13:57:48.816000+00:00] christoph7800: Anyone has an answer to this? Or is it TBD?
[2026-01-29T14:01:23.211000+00:00] anderselowsson: Could you elaborate on why they are stuffing the block? They still need to commit at the start of the slot and can use blob withholding for the free option.
[2026-01-29T14:04:49.943000+00:00] potuz: I don't think anyone can honestly know that right now. I can tell you what I suspect will happen in order of likelyhood
- The first deadline will be as short as possible. We'll test extensively and when we get good parameters we'll try to squeeze the minimum payload availability to the maximum so  that we have a maximum time for execution/proving. 
- The last deadline will be trickier to decide, reallistic blob limits will play a role and we'll want to have that deadline at least to allow that blob count. In addition reallistic gas limit will play a role since we would want to allow broadcast of pure data blocks up to that deadline. Satisfying these two parameters I believe we will try to set this deadline as early as possible to minimize the free option problem. However, given that blobs are free in this setting, I suspect that we will increase blob throughput up to the duration of the slot. I'm speculating here. 
- On the issue of penalties. I believe adding penalties in this fork will be very unlikely. They do increase complexity considerably and we are already not sure if we'll make a devnet in February. They could still be adedd/explored for next devnets, but my intuition is that we'll ship the fork with the known danger for this problem and if it happens we'll deploy penalties and other mitigations in the next fork.
[2026-01-29T15:06:01.169000+00:00] christoph7800: E.g. if the deadlines for blob release and payload release are similar and call data is cheaper you would prefer that way to prepare a free option.
```

</details>
