# P2P sync issues between Teku and Prysm payloads

**Channel:** epbs | **Date:** 2026-05-12

## Summary

The discussion centers around a P2P synchronization issue between Teku and Prysm clients where Prysm is incorrectly penalizing Teku peers. The problem stems from Prysm's penalty logic that checks if peers who return blocks also return compatible payloads - however, potuz identified a bug where Prysm penalizes peers even when they explicitly indicate they cannot provide payloads. This is particularly problematic with Teku, which frequently serves blocks but returns zero payloads, leading Prysm to penalize Teku instead of requesting payloads from other peers.

The core issue is that block and payload requests are handled as separate, independent calls rather than as a single atomic operation. While stefanbratanov suggests that penalization should occur when canonical payloads aren't served, potuz argues that non-responses (such as due to rate limiting) should be treated as "ignore" rather than penalty events. The participants acknowledge that rate limiting adds complexity since both Teku and Prysm maintain separate rate limits for blocks and payloads with the same initial limits.

The discussion ends with an unresolved question about whether Teku consistently returning blocks with zero payloads is expected behavior, with barnabasbusa suggesting this might be related to Teku downscoring Prysm peers.

## Participants

- barnabasbusa
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-12T13:19:14.098000+00:00] stefanbratanov: Ah does that have to do with the byRange Teku issues that we discussed in ACDT? Our checks while syncing payloads are quite simple, we only downscore if block's bid is based on a full payload and it doesn't exist https://gist.github.com/StefanBratanov/a268520a28040e6bfbcfd7393f6455d8
[2026-05-12T13:24:55.647000+00:00] potuz: I added code that checks if the same peer returned blocks and payloads and to penalize if they aren't compatible in any way. But the problem is that it seems that I screwed up and even if the peer told me that can't give me payloads, I penalize him for giving me blocks
[2026-05-12T13:25:16.129000+00:00] potuz: Teku often times does not serve us the payloads, so we penalize it instead of requesting the payloads from somoeone else
[2026-05-12T13:26:19.509000+00:00] potuz: One could argue that our behavior is correct though: if you return blocks then you should also return the payloads. The problem is that the two calls are independent instead of being one, so the actual correct behavior is to not penalize you if you do not reply to one of them
[2026-05-12T13:50:08.319000+00:00] stefanbratanov: I think you should penalise us if we are not serving canonical payloads to be fair
[2026-05-12T13:52:51.641000+00:00] potuz: yeah but the problem is that the call is separated
[2026-05-12T13:53:07.111000+00:00] potuz: if I send you a call to get the payloads and you don't reply then that should be an ignore
[2026-05-12T13:53:14.756000+00:00] potuz: if you're for example rate limited
[2026-05-12T14:07:11.317000+00:00] stefanbratanov: ah I see, yes, if we include rate limiting in the scenario, it becomes a bit of a mess, we keep separate rate limit with the same initial number for blocks and payloads, so it's unlikely we return partial responses from one if the other one returns everything
[2026-05-12T14:39:16.717000+00:00] potuz: we consistently see from Teku that you give us blocks and you give us zero payloads. Is this correct <@363800010518822915> ?
[2026-05-12T14:39:21.750000+00:00] potuz: I saw this a few times at least
[2026-05-12T14:44:20.017000+00:00] barnabasbusa: you think teku is downscoring prysm and thats why its giving you an empty payload?
```

</details>
