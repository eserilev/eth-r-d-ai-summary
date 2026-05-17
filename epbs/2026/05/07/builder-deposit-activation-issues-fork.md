# Builder Deposits Queue Implementation Discussion

**Channel:** epbs | **Date:** 2026-05-07

## Summary

The discussion centers on whether to implement a pending builder deposits queue to handle potential DoS attacks, with **potuz** expressing strong opposition due to concerns about introducing complex bugs during devnet stages. The main technical issues identified include race conditions between builder and validator pubkeys being inserted simultaneously, potential ordering problems, and the historical precedent that deposit handling has been a major source of consensus bugs. **potuz** advocates for simpler solutions like capping deposit numbers or using forkchoice reorgs instead.

**jtraglia** created and spec'd out a new pending deposits queue implementation (PR #5219) but ultimately decided to close it after **potuz** identified bugs and emphasized the complexity concerns. The discussion revealed that even with a shared queue approach, signature verification would still be required, which defeats the original purpose of avoiding the performance bottleneck. Current limits show 16 signatures at epoch boundaries, with uncertainty about whether 512 per block is reasonable.

The team decided to move forward without the queue implementation, acknowledging the DoS issue exists but prioritizing benchmarking to determine if it's actually a significant attack vector. **terencechain** reported a major optimization breakthrough, reducing processing time from 7 seconds to 50ms for 8192 deposits. Action items include conducting assertoor tests with various invalid signature scenarios and increasing SSZ limits, with the understanding that the expensive nature of the attack ($20M+ cost) and lack of impact on chain liveness make it a manageable risk.

## Participants

- barnabasbusa
- jtraglia
- m.kalinin
- pk910
- potuz
- terencechain
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-07T15:15:37.057000+00:00] m.kalinin: <@755590043632140352> why you don't like pending builder deposits queue? (decided to move here)
[2026-05-07T15:17:05.354000+00:00] potuz: many reasons but mostly because it's a large change in devnet stages. We will need to deal with new bugs creeping out, for example races between builder pubkeys and validator pubkeys being inserted at the same time on the queues, different ordering for these operations, etc.
[2026-05-07T15:17:41.887000+00:00] potuz: capping the number of deposits, or even reorging with forkchoice the payloads that don't seems simpler
[2026-05-07T15:18:19.711000+00:00] potuz: if we get to have to add the queue, we'll need comprehensive testing, the deposit handling was so far the place where we had the vast majority of consensus bugs merged into the spec
[2026-05-07T15:19:08.367000+00:00] m.kalinin: yeah, i see
[2026-05-07T15:20:50.228000+00:00] potuz: the race situation is not trivial: I do not see an easy way out but to anyway verify the signature immediately so that it's likely that the queue does not help at all
[2026-05-07T15:21:59.528000+00:00] m.kalinin: the race situation can be handled on dequeuing
[2026-05-07T15:22:11.951000+00:00] potuz: may be, yes
[2026-05-07T15:22:48.711000+00:00] potuz: haven't thought much about it, it does sound like a non-trivial number of bugs can be created
[2026-05-07T15:26:02.554000+00:00] potuz: can we prioritize benchmarking the builder deposit issue? perhaps getting some benches from clients on how many deposits we can actually handle on block processing, it would be good, if the current limit can be handled, then we can probably deal  with this with simpler systems
[2026-05-07T15:30:31.003000+00:00] m.kalinin: we have 16 sigs limit at epoch boundary today, we could get back to 16 per block as it was before 6110. But I don't know if having 512 per block is reasonable
[2026-05-07T15:33:40.113000+00:00] tersec: see also https://discord.com/channels/595666850260713488/1501548412242493490/1501618480112210020 for more discussion of the builder deposit signatures
[2026-05-07T15:34:03.287000+00:00] tersec: but they're fairly intensive
[2026-05-07T20:23:53.586000+00:00] jtraglia: <@425572898787426305> <@755590043632140352> <@412614104222531604> and others... I spec'd out what a new pending deposit queue might look like for builders. I sort of like it. What do y'all think? https://github.com/ethereum/consensus-specs/pull/5219
[2026-05-07T20:25:59.917000+00:00] jtraglia: I considered re-using the same `state.pending_deposits` queue, but found it to complicate things. A new queue is easier.
[2026-05-07T20:25:59.924000+00:00] barnabasbusa: we need to make sure normal 0x00,0x01,0x02 deposits and top ups for 0x02 also handled by the same/similar queue mechanism
[2026-05-07T20:26:57.031000+00:00] jtraglia: Hmm yes, this reminds me that I need to handle top-up deposits better.
[2026-05-07T20:29:30.033000+00:00] jtraglia: Ugh I'm going to need to make some bigger changes...
[2026-05-07T20:45:47.104000+00:00] tersec: why again was it so terrible to require builders to have 32 ETH?
[2026-05-07T20:46:01.571000+00:00] tersec: the original 0x03 validator design
[2026-05-07T20:56:04.401000+00:00] potuz: Don't start that one again 😆 , you'll get leverage enough to remove ePBS or trustless payments altogether. Otoh I said above why I don't like the queue <@592004585506340885>, I'll review in a bit though
[2026-05-07T20:58:56.311000+00:00] jtraglia: Ah yeah, please no...
[2026-05-07T21:17:33.700000+00:00] potuz: left the review stopping at the first bug I found
[2026-05-07T21:18:34.247000+00:00] potuz: I think these races may be solvable, but just the fact that we have to start thinking about them and probably introduce another consensus breaking or UX breaking bug scares the hell out of me. I would rather stop this by capping, or simply take the attack and let CL clients be dossed until all signature verifications pass.
[2026-05-07T21:18:57.253000+00:00] potuz: The attack itself is incredibly expensive and it does not affect liveness of the chain
[2026-05-07T21:21:48.732000+00:00] jtraglia: I mean $20m is expensive but really not that much. I suppose you want to just reduce the ssz limit?
[2026-05-07T21:22:27.357000+00:00] jtraglia: Not surprised my PR has a bug. It was done pretty quickly. Will work on fixing it.
[2026-05-07T21:23:37.096000+00:00] jtraglia: Maybe there must just be a single queue.
[2026-05-07T21:24:20.756000+00:00] jtraglia: Even if we don't go this route, it's still useful to prototype. So we know why it's bad.
[2026-05-07T21:26:44.478000+00:00] jtraglia: But this is pretty annoying to deal with too. Not sure if there's a good solution here.
[2026-05-07T21:49:28.376000+00:00] jtraglia: I've decided to close the PR. Yeah another queue is too complex.
[2026-05-07T21:56:43.569000+00:00] potuz: We can probably deal with this with a shared queue as it was originally
[2026-05-07T21:57:24.234000+00:00] potuz: But then we'll have to deal with either 1) the builder's rage or 2) scan in the same queue independently
[2026-05-07T21:57:40.279000+00:00] potuz: That's the only solution I can think of
[2026-05-07T21:57:52.044000+00:00] jtraglia: Pretty sure these would still require a signature verification though.
[2026-05-07T21:58:00.132000+00:00] jtraglia: Which is what we were trying to avoid in the first place.
[2026-05-07T21:58:18.194000+00:00] potuz: Not if the queue is shared
[2026-05-07T21:58:33.362000+00:00] potuz: Whatever is processed before wins
[2026-05-07T21:59:07.957000+00:00] jtraglia: Oh yeah. Agreed. Sorry I thought you were saying something different. I said the same here.
[2026-05-07T21:59:48.985000+00:00] potuz: Yes that's possible but the processing becomes more complicated
[2026-05-07T22:00:31.607000+00:00] potuz: I say we move on, knowing this issue exists and try to bench if it's actually an attack or not
[2026-05-07T22:00:41.230000+00:00] potuz: We need to increase the Ssz limit though
[2026-05-07T22:01:21.210000+00:00] jtraglia: Wouldn't that make things worse?
[2026-05-07T22:01:56.056000+00:00] jtraglia: <@363800010518822915> said it took 7s to process 8192 deposits.
https://discord.com/channels/595666850260713488/1501968885434421339/1501985572410097726
[2026-05-07T22:02:34.096000+00:00] terencechain: that's before my optimization though, i already solved it in the latest commit.. 50ms now
[2026-05-07T22:02:40.525000+00:00] jtraglia: Hell yeah. Okay cool.
[2026-05-07T22:03:00.177000+00:00] jtraglia: What is the optimization? What if there's an invalid signature mixed in there?
[2026-05-07T22:13:36.259000+00:00] terencechain: still benchmarking this.. but i think the best strategy is something like divide and conquer but with a cutoff to linear
[2026-05-07T22:24:44.362000+00:00] jtraglia: <@808969530608451584> <@412614104222531604> for tomorrow, could we have an assertoor test which has 8192 deposits where 1 sig is invalid, 10 sigs are invalid, 100 sigs are invalid, 1000 sigs are invalid. It should be somewhat randomized too.
[2026-05-07T22:27:56.800000+00:00] pk910: what's the reason behind a test with invalid signatures?
we've just created a test to do valid builder deposits (all unique):
```yaml
# kurtosis config snippet
assertoor_params:
  image: ethpandaops/assertoor:master
  run_stability_check: false
  run_block_proposal_check: false
  tests:
    - file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/gloas-dev/builder-deposit-spam.yaml"
      config:
        batchSize: 200
        pendingBatches: 5 # 5*200 = 1000 deposits per block
        totalDeposits: 0  # 0 = unlimited
```
[2026-05-07T22:39:22.374000+00:00] jtraglia: I was thinking some clients my try to optimize this with an aggregate signature check. But invalid signatures would force them to check them individually, which is slower.
[2026-05-07T22:41:45.983000+00:00] jtraglia: Given they are different messages (deposits), I guess that's not possible. Ignore me. I'm tired.
```

</details>
