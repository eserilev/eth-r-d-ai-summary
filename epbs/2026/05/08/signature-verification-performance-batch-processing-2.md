# Signature Verification Optimization and Precomputing

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on optimizing signature verification for deposits and addressing potential DoS attacks. Potuz proposes that the consensus layer (CL) can precompute signature verifications before the next block arrives and limit deposits only by gas limit rather than arbitrary slot limits. He suggests increasing exit churn to make attacks more expensive, operating on the principle that attack costs should be comparable to computation time required.

When barnabasbusa questions the reasonableness of processing 10k deposits in a single slot, potuz emphasizes that the focus should be on making attacks economically expensive rather than limiting functionality. The conversation reveals tension around gas limit increases now requiring benchmarking of both execution layer (EL) and consensus layer (CL) performance. Potuz notes complications from EIP-5094 and mentions that since ePBS (enshrined PBS), PTC, attestation deadlines, and DA deadlines are interconnected.

The discussion concludes with potuz committing to optimize this in Prysm for benchmarking and indicating he has developed an idea to stop the attack, which he plans to document. Key action items include implementing caching for proposer code requests and developing the anti-attack mechanism that potuz conceived.

## Participants

- barnabasbusa
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T09:56:56.193000+00:00] potuz: I think the best solution to this issue is if we're able to handle these deposits. Notice that in theory the CL *can* optimize to precompute these signature verifications before the next block arrives
[2026-05-08T09:57:08.215000+00:00] potuz: And only limit these by gas limit
[2026-05-08T09:57:45.419000+00:00] potuz: And anyway we need to make the exit churn larger to make this attack more expensive
[2026-05-08T10:04:57.791000+00:00] barnabasbusa: I don't think its reasonable to process 10k deposits in a single slot
[2026-05-08T10:05:18.434000+00:00] potuz: Cc <@363800010518822915> we probably want to change the proposer code to cache the requests as well... After all I was right that 5094 would make this more complicated lol
[2026-05-08T10:05:50.634000+00:00] potuz: It doesn't need to be reasonable, it needs to be expensive to justify the computation
[2026-05-08T10:06:20.633000+00:00] potuz: As with anything else, the cost of the attack needs to be comparable with the compute time taken
[2026-05-08T10:14:54.085000+00:00] potuz: I'll work in optimizing this on Prysm and get a benchmark
[2026-05-08T10:16:30.830000+00:00] nflaig: but you would have to optimize this indefinitely with gas limit increases
[2026-05-08T10:16:50.636000+00:00] potuz: As with anything else isn't it?
[2026-05-08T10:17:38.408000+00:00] nflaig: so far, gas limit increase was mostly about benchmarking the EL, now we also need to benchmark the CL
[2026-05-08T10:18:22.643000+00:00] potuz: The contract can revert if the tx hits a limit but that sucks for the user
[2026-05-08T10:19:22.729000+00:00] potuz: Since ePBS this is joint, PTC, attestation deadline and da deadlines are tied
[2026-05-08T10:21:46.100000+00:00] potuz: It should be easy to handle
[2026-05-08T10:26:17.664000+00:00] potuz: Ah ok, I got an idea how to stop the attack. Will write it down
```

</details>
