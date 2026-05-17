# Gas limit configuration for local block building

**Channel:** epbs | **Date:** 2026-04-16

## Summary

The discussion centers around PR #608 for execution APIs, which proposes changes to gas limit configuration for local block building. Barnabasbusa suggests implementing this change, noting that local block building will have difficulty setting gas limits without it. However, jtraglia objects, stating they're not currently modifying `engine_forkchoiceUpdated` in gloas (likely referring to a specific implementation or protocol).

The key technical issue emerges from nflaig's comment explaining that for gloas, consensus layer clients need the ability to "act as a builder" by dynamically adjusting gas limits per payload/slot based on proposer preferences received over the peer-to-peer network. The current approach of using static gas limit configuration via CLI flags is insufficient for this use case.

The discussion leaves the implementation decision unresolved, with tersec questioning the timing of the proposal. The core tension appears to be between maintaining current API boundaries versus enabling the dynamic gas limit adjustment needed for consensus clients to effectively perform builder functions.

## Participants

- barnabasbusa
- jtraglia
- nflaig
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-16T20:28:26.606000+00:00] barnabasbusa: btw do we wanna do something like this: https://github.com/ethereum/execution-apis/pull/608
[2026-04-16T20:29:12.961000+00:00] barnabasbusa: otherwise local block building will struggle to set the gas limit
[2026-04-16T20:37:04.514000+00:00] jtraglia: I don't think so. We're not currently touching engine_forkchoiceUpdated in gloas.
[2026-04-16T20:39:02.321000+00:00] tersec: seems random, why now?
[2026-04-16T21:13:47.824000+00:00] nflaig: https://github.com/ethereum/execution-apis/pull/608#issuecomment-3798868091 last part of my comment
> for gloas, it's important to revisit this topic as a CL client can "act as a builder" but to do that it needs to be able to adjust the gas limit per payload/slot based on the proposer preferences received over p2p, a static gas limit config via CLI flag will not work here
```

</details>
