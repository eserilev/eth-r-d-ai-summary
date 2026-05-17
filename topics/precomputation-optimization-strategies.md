# Precomputation and Optimization Strategies

---

## 2026-05-08 (epbs)

The discussion centers on optimizing deposit handling and mitigating potential attacks through precomputation strategies. Potuz suggests that the consensus layer (CL) can optimize by precomputing signature verifications for deposits before the next block arrives, limiting them only by gas constraints rather than arbitrary processing limits. While barnabasbusa expresses concern about processing 10,000 deposits in a single slot being unreasonable, potuz argues that the focus should be on making attacks expensive enough to justify the computational cost rather than setting artificial limits.

The conversation reveals that EIP-5094 has introduced additional complexity to proposer code, particularly around caching requests. Potuz identifies that precomputation can occur during the "dead time" of slots on the CL side, making it feasible to handle larger deposit volumes. The discussion also touches on the need to increase exit churn limits to make potential attacks more costly.

**Action items:** Potuz commits to optimizing this functionality in Prysm and providing benchmarks, while also working on a solution to prevent the discussed attack vector. The team needs to modify proposer code to implement request caching as part of the optimization strategy.

**Participants:** barnabasbusa, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T09:56:56.193000+00:00] potuz: I think the best solution to this issue is if we're able to handle these deposits. Notice that in theory the CL *can* optimize to precompute these signature verifications before the next block arrives
[2026-05-08T09:57:08.215000+00:00] potuz: And only limit these by gas limit
[2026-05-08T09:57:45.419000+00:00] potuz: And anyway we need to make the exit churn larger to make this attack more expensive
[2026-05-08T10:04:57.791000+00:00] barnabasbusa: I don't think its reasonable to process 10k deposits in a single slot
[2026-05-08T10:05:18.434000+00:00] potuz: Cc <@363800010518822915> we probably want to change the proposer code to cache the requests as well... After all I was right that 5094 would make this more complicated lol
[2026-05-08T10:05:50.634000+00:00] potuz: It doesn't need to be reasonable, it needs to be expensive to justify the computation
[2026-05-08T10:06:20.633000+00:00] potuz: As with anything else, the cost of the attack needs to be comparable with the compute time taken
[2026-05-08T10:14:54.085000+00:00] potuz: I'll work in optimizing this on Prysm and get a benchmark
[2026-05-08T10:21:37.177000+00:00] potuz: The precompute on the CL side is in the dead time of the slot
[2026-05-08T10:21:46.100000+00:00] potuz: It should be easy to handle
[2026-05-08T10:26:17.664000+00:00] potuz: Ah ok, I got an idea how to stop the attack. Will write it down
```

</details>

