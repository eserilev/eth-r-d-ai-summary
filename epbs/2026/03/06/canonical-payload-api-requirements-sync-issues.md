# Canonical payload API requirements and sync issues

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers on a sync issue in Prysm caused by serving non-canonical payloads through the API. Potuz explains that peers should only serve canonical payloads, and the root cause involved both Prysm incorrectly applying a non-canonical payload and another client (either Lighthouse or Prysm) improperly serving it in the first place. He suggests that peers serving non-canonical payloads should be downscored and requests this requirement be added to API specifications if not already present.

The conversation raises questions about payload filtering behavior, specifically whether clients serve all payloads or only follow the canonical hash chain in "payload by range" requests. There's acknowledgment of a trade-off where filtering non-canonical data could create DoS vulnerabilities if clients retain too much non-canonical data. Additionally, 0xunclebill notes they may not be properly pruning non-canonical payload envelopes after finalization, which could contribute to the observed issues.

The discussion leaves several questions unresolved, including clarification on whether the issue affects "by range" or "by root" requests, confirmation of current payload serving behavior across clients, and whether downscoring mechanisms are properly implemented for this scenario.

## Participants

- 0xunclebill
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T22:39:16.506000+00:00] potuz: I know I promised not to get into API discussions, but Friday night's wine is for this. We should agree that the Payloads should be only the canonical ones
[2026-03-06T22:39:28.092000+00:00] potuz: This is what breaks sync in Prysm
[2026-03-06T22:39:50.510000+00:00] potuz: Or what broke it when <@412614104222531604> was restarting it
[2026-03-06T22:40:25.400000+00:00] potuz: We should have downscored the peer that served that (cc <@737658693331714159> <@1394302418661015606> )
[2026-03-06T22:41:42.058000+00:00] potuz: So Prysm's bug was to apply the Payload, but the peer's bug (either lighthouse or prysm) was to even give it out
[2026-03-06T22:42:03.932000+00:00] potuz: If it's not in the API specs please add this condition
[2026-03-06T23:08:16.049000+00:00] potuz: Can you confirm the behavior in the Payload by range BTW?
[2026-03-06T23:08:33.468000+00:00] potuz: Do you serve all or just walk back the hash chain
[2026-03-06T23:09:13.096000+00:00] potuz: It's a trade-off if clients keep non canonical data we may be dossed in the filtering
[2026-03-06T23:09:43.613000+00:00] potuz: But it's weird that the reply is different than for blocks
[2026-03-06T23:11:09.617000+00:00] 0xunclebill: I’ll check in a bit. We also might not be pruning non canonical payload envelopes post finalization  which could be contributing to the weirdness
[2026-03-06T23:29:50.127000+00:00] terencechain: is that by range or by root? i dont think we downscore either
```

</details>
