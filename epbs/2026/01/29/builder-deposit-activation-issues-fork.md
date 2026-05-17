# Builder Deposits and Fork Activation Timing

**Channel:** epbs | **Date:** 2026-01-29

## Summary

The discussion centers on the timing mechanics for builder activation during a fork transition. Potuz seeks confirmation that if deposits are "flooded" pre-fork and builders deposit 2 epochs before the fork, they would become active builders right at fork activation. 

Jtraglia confirms this is correct but notes two important caveats: it wouldn't work if the deposit queue is empty or if the slot at fork_slot-2 fails to be finalized. When Potuz emphasizes the "flood" scenario, jtraglia agrees the timing would work as expected, but points out that flooding requires 512 ETH worth of deposits, which creates a significant financial barrier that not everyone could meet.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T13:04:00.358000+00:00] potuz: Can someone fact check me? If we do flood pre-fork with deposits, and builders deposit 2 epochs in advance from the fork, they will be onboarded as active builders right at the fork correct?
[2026-01-29T13:43:41.630000+00:00] jtraglia: Not if the deposit queue is empty or something prevents fork_slot-2 from being finalized.
[2026-01-29T13:49:56.176000+00:00] potuz: the keyword is "flood"
[2026-01-29T13:55:43.511000+00:00] jtraglia: Okay then yeah. That’s right. If the queue is empty you’d need to flood with 512 ETH worth of deposits, which not anyone could do.
```

</details>
