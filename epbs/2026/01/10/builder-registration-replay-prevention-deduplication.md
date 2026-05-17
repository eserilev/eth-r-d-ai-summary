# Builder Registration Replay Prevention and Deduplication

**Channel:** epbs | **Date:** 2026-01-10

## Summary

The discussion centers on whether builder registrations need replay prevention mechanisms, similar to those used for bid requests. While potuz initially argued that fake registration replays aren't problematic, they identified a specific attack vector where malicious builders could replay registrations with different builder-specific preferences (like trusted payment limits) to competitors, causing them to build blocks that proposers would ultimately reject.

nero_eth emphasized that wasted computational work from replayed registrations is a significant concern, as builders currently only engage in the resource-intensive block building process when they know the validator is connected to their relay. potuz countered that builders already build multiple blocks per slot for future use, making this less concerning, but acknowledged the attack potential when proposers set different parameters (like 1ETH max payment for one builder vs 2ETH for another).

The conversation concludes with agreement that adding deduplication to registrations could provide valuable flexibility for per-builder preferences, even if not immediately necessary. The technical trade-off appears to be between the computational overhead of signature verification versus preventing both resource waste and preference manipulation attacks.

## Participants

- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-10T05:00:49.191000+00:00] nero_eth: Wasn't it you arguing earlier that we should have them to prevent regs being replayed to other builders to make them do work (build a block) for nothing?
Felt like it was you that convinced me the lack of dedub in the message is worth it 😁
[2026-01-10T09:27:35.845000+00:00] potuz: This is on the bid request and that's a request from builders yes
[2026-01-10T09:29:12.247000+00:00] potuz: Registrations don't really need it, at least I don't see what's the real problem with fake replays
[2026-01-10T09:35:59.206000+00:00] potuz: Although if the signing is not so much of a problem, adding it to the registration is also worth it for the following situation: a proposer has different builder preferences for different builders. And replays could make one builder not know what are the real preferences for them, making them prepare a payload that will be rejected
[2026-01-10T11:22:50.285000+00:00] nero_eth: As a builder, I would forward the registration I got from the proposer to other builders. Builders have no way to tell if the registration is really supposed for them, so, they would build a block and engage in the competition. The proposer would never pull directly from those, waisted work, and they would still stend bids over the p2p, consuming resources(?).
[2026-01-10T11:28:07.839000+00:00] potuz: the wasted work I believe it's irrelevant: builders anyway build multiple blocks per slot, they will need to build for this slot even if the current proposer doesn't call them, because during N+1 they need to build on top of N, so this part doesn't worry me at all. The other attack of replaying registrations may be worse cause if the proposer sent a registration with a max trusted payment of say 1ETH to builder 1 and 2ETH to builder 2. Builder 2 can try to force builder 1 to build a block with a trusted payment of 1.5ETH that the proposer will reject.
[2026-01-10T11:49:08.684000+00:00] nero_eth: I don't think the wasted work is irrelevant. Today, builders check if the validator is connect to a relay they are connected to and only then engage in block building. 
Engaging in building (which is great workload thus costly) is different from just processing one block as part of the stf to then continue with N+1.
And yeah, the per-builder registration adds flexibility, even though it might not be (initially) needed.
```

</details>
