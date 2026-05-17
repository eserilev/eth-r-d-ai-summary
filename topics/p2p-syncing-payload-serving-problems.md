# P2P Syncing Issues and Payload Serving Problems

---

## 2026-05-12 (epbs)

The discussion centers on P2P syncing issues between Ethereum clients, specifically problems with Teku's payload serving behavior. Potuz identified a bug in their penalty system where peers are incorrectly penalized for not serving payloads even when they've indicated they cannot provide them. The core issue is that block requests and payload requests are separate independent calls, making it difficult to properly handle scenarios where a peer can serve blocks but not payloads due to rate limiting or other constraints.

Stefan from Teku confirmed they use separate rate limits for blocks and payloads with the same initial limits, making partial responses unlikely under normal conditions. However, Potuz reports consistently observing Teku nodes serving blocks while providing zero payloads. The conversation reveals broader frustration with P2P syncing complexity, with Potuz expressing regret about not implementing EIP-8237 alongside EIP-5094, suggesting that combining block and payload requests into a single call would have avoided these coordination issues.

The discussion leaves open whether Teku's behavior of serving blocks without payloads is intentional or a bug, with Potuz seeking clarification from the Teku team about this observed pattern.

**Participants:** potuz, stefanbratanov

<details>
<summary>Raw messages</summary>

```
[2026-05-12T13:19:14.098000+00:00] stefanbratanov: Ah does that have to do with the byRange Teku issues that we discussed in ACDT? Our checks while syncing payloads are quite simple, we only downscore if block's bid is based on a full payload and it doesn't exist https://gist.github.com/StefanBratanov/a268520a28040e6bfbcfd7393f6455d8
[2026-05-12T13:24:55.647000+00:00] potuz: I added code that checks if the same peer returned blocks and payloads and to penalize if they aren't compatible in any way. But the problem is that it seems that I screwed up and even if the peer told me that can't give me payloads, I penalize him for giving me blocks
[2026-05-12T13:25:16.129000+00:00] potuz: Teku often times does not serve us the payloads, so we penalize it instead of requesting the payloads from somoeone else
[2026-05-12T13:26:19.509000+00:00] potuz: One could argue that our behavior is correct though: if you return blocks then you should also return the payloads. The problem is that the two calls are independent instead of being one, so the actual correct behavior is to not penalize you if you do not reply to one of them
[2026-05-12T13:50:08.319000+00:00] stefanbratanov: I think you should penalise us if we are not serving canonical payloads to be fair
[2026-05-12T13:52:51.641000+00:00] potuz: yeah but the problem is that the call is separated
[2026-05-12T13:53:07.111000+00:00] potuz: if I send you a call to get the payloads and you don't reply then that should be an ignore
[2026-05-12T13:53:14.756000+00:00] potuz: if you're for example rate limited
[2026-05-12T13:53:48.502000+00:00] potuz: if it were a unique call then it would be simpler... every single day since Svalbard I regret not fighting stronger for 8237
[2026-05-12T13:54:07.084000+00:00] potuz: essentially ALL problems we see on devnets steer from peering and boil down to syncing complexity
[2026-05-12T13:54:31.441000+00:00] potuz: it was utterly stupid not to bag 8237 with 5094
[2026-05-12T14:07:11.317000+00:00] stefanbratanov: ah I see, yes, if we include rate limiting in the scenario, it becomes a bit of a mess, we keep separate rate limit with the same initial number for blocks and payloads, so it's unlikely we return partial responses from one if the other one returns everything
[2026-05-12T14:39:16.717000+00:00] potuz: we consistently see from Teku that you give us blocks and you give us zero payloads. Is this correct <@363800010518822915> ?
[2026-05-12T14:39:21.750000+00:00] potuz: I saw this a few times at least
```

</details>

