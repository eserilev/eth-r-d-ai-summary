# Second Queue Implementation Pr Discussion

Implementation of secondary queue system and pull request coordination

## Discussions
- [[epbs/2026/05/08/second-queue-implementation-pr-discussion]]
---

## 2026-05-08 (epbs)

The discussion centers around implementing a second queue for Ethereum's deposit system, triggered by m.kalinin suggesting that a previously closed PR was closed prematurely. Kalinin argues that a second queue is necessary because the first queue has activation queue limitations, and proposes that validation checks should be performed during dequeuing to avoid conflicts. He opens an alternative PR (#5221) to address these issues.

Barnabasbusa raises concerns about the proposed limit of 16 deposits, arguing it's too restrictive and could enable griefing attacks where an attacker with just 32 ETH could monopolize the queue by continuously depositing and exiting validators. The discussion reveals a cost analysis disagreement - while Kalinin calculates the attack cost at 64*32*MAX_DEPOSIT_REQUESTS_PER_PAYLOAD ETH due to the 64-epoch withdrawability delay, barnabasbusa argues it would only cost 1,024 ETH (64*16*1 ETH), making such attacks economically feasible.

The conversation also touches on unresolved issues with top-up deposits, where 8193x1ETH top-up requests could still cause problems. The discussion ends with clarification about different limiting mechanisms - whether to limit deposits per payload (16) or maintain higher limits (512) while introducing a separate queue system.

**Participants:** barnabasbusa, m.kalinin

<details>
<summary>Raw messages</summary>

```
[2026-05-08T05:02:27.114000+00:00] m.kalinin: i think we need a second queue, <@592004585506340885> you closed your PR too early 😄 
the second queue makes sense as the first one has the activation queue limitation, i don't think we want to have a different scan over the same queue as it's the complexity while the semantics similar to the first queue. I believe all the checks should be done upon dequeuing then there shouldn't be any conflict as far as i can think of
[2026-05-08T05:02:35.130000+00:00] m.kalinin: i'll try to open a PR
[2026-05-08T05:09:26.079000+00:00] m.kalinin: > Whenever there can be a race we need to validate signatures at the insertion point, which defeats the purpose entirely of the extra queue.
Why at the insertion point?
[2026-05-08T08:28:37.480000+00:00] m.kalinin: Alternative PR: https://github.com/ethereum/consensus-specs/pull/5221
[2026-05-08T08:29:13.865000+00:00] barnabasbusa: 16 is extremly limiting
[2026-05-08T08:29:26.301000+00:00] barnabasbusa: that means with 32 eth you can hog that queue non stop?
[2026-05-08T08:29:56.750000+00:00] barnabasbusa: you setup a dummy bot and keep depositing 16 new builders, then exit them in the following epoch then rinse and repeat
[2026-05-08T08:30:38.367000+00:00] barnabasbusa: also none of this fix the top up deposit issue
[2026-05-08T08:30:57.287000+00:00] m.kalinin: what is the issue?
[2026-05-08T08:30:59.604000+00:00] barnabasbusa: where we still gonna be shit out of luck if anyone does  8193x1ETH top up requests
[2026-05-08T08:31:12.520000+00:00] m.kalinin: ah, that's a different thing
[2026-05-08T08:31:17.440000+00:00] barnabasbusa: yeah
[2026-05-08T08:48:47.103000+00:00] m.kalinin: `MIN_BUILDER_WITHDRAWABILITY_DELAY=64`, so it's there going to be 64 epochs to wait till you can withdraw, if you don't want to wait then you have to have a budget for 64 epochs, which is `64*32*MAX_DEPOSIT_REQUESTS_PER_PAYLOAD` ETH, looks quite expensive. What is the benefit of such an attack?
[2026-05-08T08:49:27.237000+00:00] barnabasbusa: `64*16*1`ETH no?
[2026-05-08T08:49:33.689000+00:00] barnabasbusa: thats only 1024 ETH
[2026-05-08T08:49:53.792000+00:00] m.kalinin: 16 per block
[2026-05-08T08:50:05.475000+00:00] barnabasbusa: `MAX_PENDING_BUILDER_DEPOSITS_PER_EPOCH`
[2026-05-08T08:50:09.643000+00:00] barnabasbusa: this is the new flag you proposed
[2026-05-08T08:50:16.113000+00:00] barnabasbusa: per_epoch
[2026-05-08T08:50:51.141000+00:00] m.kalinin: this is a different thing, if we limit to 16 per payload, not to 512 without introducing a separate queue
```

</details>

