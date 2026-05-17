# Attestation Timing and Builder Synchrony Requirements

**Channel:** epbs | **Date:** 2026-05-07

## Summary

The discussion centers around timing requirements for attestations and builders in Ethereum's consensus mechanism, triggered by a GitHub PR that proposes changes to current timing assumptions. m.kalinin raises concerns that the PR requires stronger synchrony assumptions compared to the existing design, specifically reducing the timing delta from 3 seconds to 1.5 seconds, and questions whether this will work even with builders being "super nodes."

potuz clarifies that builders never actually had 3 seconds to listen to attestations, noting that the 90th percentile for attestations is around 1 second and sophisticated builders need even less time to broadcast. They also mention hopes to move the attestation deadline to 2 seconds and emphasize that all block production timings are currently tentative and should be determined through benchmarking rather than assumptions.

The key takeaway is that potuz is primarily focused on implementing independent deadlines in the system, with the actual timing values (which could be as high as 9 seconds initially) to be determined through proper benchmarking and testing rather than theoretical constraints.

## Participants

- jimmygchen
- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-07T02:01:28.303000+00:00] jimmygchen: This: https://github.com/ethpandaops/eth-beacon-genesis/pull/77
[2026-05-07T05:59:23.429000+00:00] m.kalinin: Today we have attestations released at 3s, then 3s for builder to wait for attestations, then 3s for payload propagation. This PR requires a stronger synchrony assumption than the one we have in today's design. It's simply 1.5s delta comparing to 3s. Is this gonna work because builder is a super node?
[2026-05-07T09:48:47.197000+00:00] potuz: At no point we had timings so that the builder had 3 seconds to listen to attestations. The p90 for attestations is in 1 second, a sophisticated builder needs less than that to broadcast. I'm also hoping that attestation deadline will move to 2 seconds. At any rate all bps are just tentative at this point and we should benchmark to decide the actual numbers. What I'm mostly interested is in that we have the independent deadlines implemented. They could both be 9 seconds now for all I know
```

</details>
