# Signature Verification Performance Benchmarks

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on performance benchmarks for signature verification in Ethereum, specifically focusing on batch verification of 8192 signatures. Terencechain shared Prysm benchmarks showing ~640ms for all valid signatures on Apple M1 Max, but performance degrades significantly with invalid signatures (1.35s for 1 bad signature, 2.13s for 8 bad signatures), with mid-high end hardware expected to be 1-2x slower. The bottleneck appears to be primarily G1 multiplication operations in tight loops.

Potuz suggested optimizing by forking blst to add a Go assembly wrapper that bypasses CGO calls, though this approach carries risks since blst handles more than just elliptic curve operations. The team also discussed limitations with builder deposits, finding they can handle ~7200 builder deposits with 200M gas (not the previously estimated 9000), while top-up deposits can exceed 8192 but face different SSZ-related constraints. There's consensus that precomputation optimizations are needed for both processing and proposing paths.

**Action items identified:** jtraglia will create PRs to increase builder withdrawability delays and SSZ size limits, while client teams should focus on optimizing large builder deposit handling. However, the team agreed to prioritize stabilizing the current ePBS implementation before conducting further stress testing on larger non-finality devnets.

## Participants

- barnabasbusa
- jtraglia
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T13:24:54.950000+00:00] terencechain: This is prysm. 99% of it is just multiplication on g1 and million loops. 8192 times
```
(DC8 + parallel pubkey batch verify), N=8192, Apple M1 Max:
  ┌──────────────────────────────────┬─────────┐
  │             Scenario             │  Time   │
  ├──────────────────────────────────┼─────────┤
  │ Happy (all valid)                │ ~640 ms │
  ├──────────────────────────────────┼─────────┤
  │ 1 bad signature                  │ ~1.35 s │
  ├──────────────────────────────────┼─────────┤
  │ 8 bad signatures (evenly spread) │ ~2.13 s │
```
[2026-05-08T13:25:31.456000+00:00] terencechain: 600ms on M1 Max is probably 1-2s on a mid-high end NUC
[2026-05-08T14:20:06.436000+00:00] potuz: If the issue is the loop itself I'm pretty sure we can get rid of this by forking blst
[2026-05-08T14:20:50.756000+00:00] potuz: I could try opening a PR for the go bindings on blst but I doubt they'll accept them
[2026-05-08T14:21:20.981000+00:00] jtraglia: 0% chance lol
[2026-05-08T14:21:42.492000+00:00] terencechain: Then we might have to fork? 
What was the verdict from the party lounge? 
Sorry I was driving
[2026-05-08T14:22:02.582000+00:00] barnabasbusa: So right now I'd focus on fixing other aspects of glamsterdam, and once we are stable we can stress test these builder deposits in a larger non finality devnet scenario
[2026-05-08T14:22:20.952000+00:00] potuz: The fix is simple though. Just add a goassembly wrapper that jumps directly to the assembly instead of using cgo to call it
[2026-05-08T14:22:53.067000+00:00] barnabasbusa: 
[2026-05-08T14:22:59.538000+00:00] barnabasbusa: chain looks pretty cooked with 6656 builder deposits
[2026-05-08T14:23:08.433000+00:00] potuz: Only problem is that blst does other things besides just EC manipulation so it may be dangerous.
[2026-05-08T14:24:12.304000+00:00] barnabasbusa: so I was wrong in the call where I said we could do 9000 deposits with 200m gas. 

We can do ~7200 builder deposits only with 200M gas 
We can still do over 8192 top up deposits with 200M gas tho
[2026-05-08T14:24:15.844000+00:00] potuz: We need to precompute this
[2026-05-08T14:24:44.398000+00:00] potuz: Topups are not a problem I believe
[2026-05-08T14:25:00.289000+00:00] barnabasbusa: topups will be a problem if they exceed the 8192, its a different kind of problem, its the ssz problem
[2026-05-08T14:25:02.382000+00:00] terencechain: Topups don't verify signature
[2026-05-08T14:25:35.979000+00:00] potuz: But the important point here is that we still need to optimize by precomputing this both in the processing and the proposing paths. Almost undoing 5094 lol
[2026-05-08T14:27:30.875000+00:00] barnabasbusa: state of the chain
[2026-05-08T14:27:48.265000+00:00] barnabasbusa: Anyways, as mentioned before, testing this right now when epbs implementation is still so fragile is worthless.
[2026-05-08T14:27:58.185000+00:00] barnabasbusa: We need to cycle back to this when we are doing the hardening
[2026-05-08T14:28:36.884000+00:00] barnabasbusa: however, 2 things are certain, we gotta increase the withdrawability delay of the builders, and increase the ssz size to maxint/or yeet it completely with stable ssz
[2026-05-08T14:28:49.589000+00:00] jtraglia: I'll make PRs for these.
[2026-05-08T14:29:11.356000+00:00] barnabasbusa: In the meantime client teams should work on optimizing large builder deposits
```

</details>
