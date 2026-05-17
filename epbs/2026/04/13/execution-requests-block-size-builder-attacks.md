# Execution requests block size analysis and builder attacks

**Channel:** epbs | **Date:** 2026-04-13

## Summary

nflaig shared mainnet data analysis on execution requests block size impact, finding that 91.2% of blocks have zero execution requests with an average of ~29 bytes per block and a worst case of ~14 KB for 74 deposits in one block. They concluded this appears negligible for storage concerns.

potuz raised a concern about potential builder attacks where malicious builders could include many invalid execution requests at no cost to themselves, creating a burden for the next proposer to process. When nflaig suggested using gossip checks against the root in the bid as a prevention mechanism, potuz clarified that even with a valid root, builders can still gossip the maximum number of invalid deposit requests - something that currently hurts the builder but under the proposed changes would instead hurt the next proposer.

The discussion identifies an unresolved attack vector where the cost burden shifts from malicious builders to subsequent proposers, suggesting this builder attack scenario requires further consideration in the proposal design.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T21:23:55.334000+00:00] nflaig: was curious to get some mainnet data on this and sampled the last 2 weeks worth of blocks to get a better sense of whether or not adding `parent_execution_requests` to the block is big deal, some data points worth noting
- 91.2% of blocks have zero execution requests
- the average is ~29 bytes/block
- the worse case was ~14 KB extra data (74 deposits in one block)
so I do think this is almost negligible, especially in terms of extra storage, but definitely worth to analyse further, also wrt to what the limiting factor here, I doubt we can even reach the current ssz limit without bumping the gas limit?
[2026-04-13T21:25:45.156000+00:00] potuz: The average case is not the problem. The issue is if a builder can cause an ex ante for example by including a ton of invalid requests. It's free for them and broadcast is not an issue for the Payload, but it's costly to the next proposer
[2026-04-13T21:26:30.903000+00:00] nflaig: can that be prevented by having a gossip check against the root in the bid?
[2026-04-13T22:10:35.117000+00:00] potuz: The root can be fine
[2026-04-13T22:10:57.841000+00:00] potuz: It's perfectly fine to gossip the max number of deposit requests that are crap
[2026-04-13T22:11:07.298000+00:00] potuz: Builders can do this today but it hurts them
[2026-04-13T22:11:21.966000+00:00] potuz: Whether with your PR they'll hurt the next proposer
```

</details>
