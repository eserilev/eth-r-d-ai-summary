# Builder Timing and Synchrony Assumptions

---

## 2026-05-07 (epbs)

The discussion centers around a GitHub PR that proposes changes to builder timing assumptions in Ethereum's consensus mechanism. M.kalinin raises concerns that the PR requires stronger synchrony assumptions compared to the current design, specifically reducing the time delta from 3 seconds to 1.5 seconds, and questions whether this will work even with builders being "super nodes."

Potuz pushes back on the timing characterization, clarifying that builders never actually had 3 seconds to listen for attestations - the p90 for attestations is around 1 second, and sophisticated builders need less time than that to broadcast. He also mentions hopes to move the attestation deadline to 2 seconds and emphasizes that all the current timings are tentative and should be determined through benchmarking.

The key takeaway is that while specific timing values remain to be determined through performance testing, the main goal is implementing independent deadlines in the system. Potuz notes that the actual deadline values could even be set to 9 seconds initially, with the primary focus being on having the independent deadline mechanism in place rather than the specific timing parameters.

**Participants:** jimmygchen, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-07T02:01:28.303000+00:00] jimmygchen: This: https://github.com/ethpandaops/eth-beacon-genesis/pull/77
[2026-05-07T05:59:23.429000+00:00] m.kalinin: Today we have attestations released at 3s, then 3s for builder to wait for attestations, then 3s for payload propagation. This PR requires a stronger synchrony assumption than the one we have in today's design. It's simply 1.5s delta comparing to 3s. Is this gonna work because builder is a super node?
[2026-05-07T09:48:47.197000+00:00] potuz: At no point we had timings so that the builder had 3 seconds to listen to attestations. The p90 for attestations is in 1 second, a sophisticated builder needs less than that to broadcast. I'm also hoping that attestation deadline will move to 2 seconds. At any rate all bps are just tentative at this point and we should benchmark to decide the actual numbers. What I'm mostly interested is in that we have the independent deadlines implemented. They could both be 9 seconds now for all I know
```

</details>

