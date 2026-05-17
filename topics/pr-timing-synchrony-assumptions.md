# PR timing and synchrony assumptions discussion

---

## 2026-05-07 (epbs)

The discussion centers around timing requirements and synchrony assumptions for a PR that affects Ethereum's consensus mechanism. M.kalinin raised concerns that the PR requires stronger synchrony assumptions than the current design, which allocates 3 seconds each for attestation release, builder waiting time, and payload propagation, compared to the PR's proposed 1.5 second delta.

Potuz clarified that builders never actually had a full 3 seconds to listen to attestations, noting that the p90 for attestations is around 1 second and sophisticated builders can broadcast in less time. He also mentioned expectations for the attestation deadline to move to 2 seconds and emphasized that all current benchmarks are tentative.

The key consensus appears to be that actual timing parameters should be determined through benchmarking rather than theoretical assumptions. Potuz indicated the primary focus is implementing independent deadlines in the system, with the specific timing values (whether 1.5s, 3s, or even 9s) to be determined later based on empirical testing.

**Participants:** jimmygchen, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-07T02:01:28.303000+00:00] jimmygchen: This: https://github.com/ethpandaops/eth-beacon-genesis/pull/77
[2026-05-07T05:59:23.429000+00:00] m.kalinin: Today we have attestations released at 3s, then 3s for builder to wait for attestations, then 3s for payload propagation. This PR requires a stronger synchrony assumption than the one we have in today's design. It's simply 1.5s delta comparing to 3s. Is this gonna work because builder is a super node?
[2026-05-07T09:48:47.197000+00:00] potuz: At no point we had timings so that the builder had 3 seconds to listen to attestations. The p90 for attestations is in 1 second, a sophisticated builder needs less than that to broadcast. I'm also hoping that attestation deadline will move to 2 seconds. At any rate all bps are just tentative at this point and we should benchmark to decide the actual numbers. What I'm mostly interested is in that we have the independent deadlines implemented. They could both be 9 seconds now for all I know
```

</details>

