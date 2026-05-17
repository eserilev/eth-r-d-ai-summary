# Engine API Gas Limit Configuration

---

## 2026-04-16 (epbs)

The discussion centers around PR #608 for the Engine API, which proposes adding gas limit configuration capabilities. Barnabasbusa raised the issue that without this functionality, local block building will struggle to set appropriate gas limits. However, jtraglia opposed the change, noting that they're not currently modifying `engine_forkchoiceUpdated` in the gloas implementation, while tersec questioned the timing of the proposal.

The key technical concern, clarified by nflaig, is that for gloas (presumably a consensus layer client), the ability to dynamically adjust gas limits is crucial when the client acts as a builder. The client needs to modify gas limits per payload/slot based on proposer preferences received over the peer-to-peer network, which cannot be achieved with static CLI configuration flags. This highlights a fundamental limitation in the current Engine API when consensus layer clients take on builder responsibilities and need dynamic gas limit control based on real-time network conditions.

**Participants:** barnabasbusa, jtraglia, nflaig, tersec

<details>
<summary>Raw messages</summary>

```
[2026-04-16T20:28:26.606000+00:00] barnabasbusa: btw do we wanna do something like this: https://github.com/ethereum/execution-apis/pull/608
[2026-04-16T20:29:12.961000+00:00] barnabasbusa: otherwise local block building will struggle to set the gas limit
[2026-04-16T20:37:04.514000+00:00] jtraglia: I don't think so. We're not currently touching engine_forkchoiceUpdated in gloas.
[2026-04-16T20:39:02.321000+00:00] tersec: seems random, why now?
[2026-04-16T21:13:47.824000+00:00] nflaig: https://github.com/ethereum/execution-apis/pull/608#issuecomment-3798868091 last part of my comment
> for gloas, it's important to revisit this topic as a CL client can "act as a builder" but to do that it needs to be able to adjust the gas limit per payload/slot based on the proposer preferences received over p2p, a static gas limit config via CLI flag will not work here
```

</details>

