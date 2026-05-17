# Signature verification performance benchmarking

---

## 2026-05-08 (epbs)

The discussion focused on signature verification performance benchmarking using Prysm with 8192 signature verifications involving G1 multiplication operations. Terence presented benchmark results on an Apple M1 Max showing ~640ms for all valid signatures, but performance degrading significantly with invalid signatures: ~1.35s for 1 bad signature and ~2.13s for 8 bad signatures evenly distributed. He noted that the 600ms M1 Max performance would likely translate to 1-2 seconds on mid-to-high end NUC hardware.

Barnabas shifted focus to the broader Glamsterdam testnet, suggesting priority should be given to stabilizing other aspects before stress testing builder deposits in larger non-finality scenarios. He reported that the chain appeared problematic ("pretty cooked") when handling 6656 builder deposits, indicating potential scalability concerns that need to be addressed before advancing to more intensive testing phases.

**Participants:** barnabasbusa, terencechain

<details>
<summary>Raw messages</summary>

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
[2026-05-08T14:22:02.582000+00:00] barnabasbusa: So right now I'd focus on fixing other aspects of glamsterdam, and once we are stable we can stress test these builder deposits in a larger non finality devnet scenario
[2026-05-08T14:22:59.538000+00:00] barnabasbusa: chain looks pretty cooked with 6656 builder deposits
```

</details>

