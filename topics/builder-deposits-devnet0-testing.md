# Builder deposits on devnet0 testing

---

## 2026-03-18 (epbs)

terencechain asked whether builder deposits have been tested on devnet0, noting that clients have already passed the relevant specification tests for this functionality. pk910 responded that while builder deposits haven't been tested specifically on devnet0, they have been successfully tested on local development networks.

The discussion leaves an open question about whether builder deposit testing should proceed on devnet0, given that the functionality has been validated locally and clients are passing spec tests.

**Participants:** pk910, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-18T01:20:02.595000+00:00] terencechain: have we tried deposit a builder on devnet0? assuming clients already passed spec tests for this
[2026-03-18T02:58:57.939000+00:00] pk910: not on devnet0,  but we've done it in local devnets
```

</details>

---

## 2026-05-06 (epbs)

pk910 raised concerns about a potential denial-of-service vector in the planned ePBS (enshrined Proposer-Builder Separation) implementation. With the upcoming gas limit increases to 100M+, the system would allow up to 8,192 builder deposits (totaling 193M gas) to be processed immediately in a single slot. This represents a significant change from the current deposit flow, where signature verification is deferred and rate-limited through a deposit queue.

The core issue is that validators and builders may need to perform up to 8,192 signature verifications within a single slot in worst-case scenarios, which could create performance bottlenecks or be exploited as a DOS attack. pk910 proposed a potential solution where only balance top-ups for existing builders would bypass the queue, while new builder registrations would remain rate-limited through the normal deposit queue process.

However, this approach would have the notable downside of preventing new builders from being available at the fork boundary when ePBS activates. The discussion appears to be seeking input on whether this tradeoff is acceptable or if alternative solutions should be considered to address the signature verification scalability concern.

**Participants:** pk910

<details>
<summary>Raw messages</summary>

```
[2026-05-06T11:37:18.899000+00:00] pk910: Moving over a conversation from the Interop Repricings channel:

With ePBS and planned gas limit increases to 100M+, we now allow up to 8192 builder deposits (193M gas) in a single slot to be processed immediately. Unlike the old deposit flow (where sig verification was deferred/rate-limited via the deposit queue), this means validators/builders may need to perform up to 8192 signature verifications in-slot in the worst case.

This sounds like a lot and might be a dos vector,  so the question is whether new builders should really bypass the queue, or whether only balance top-ups should bypass it while new builder registrations remain rate-limited through the normal deposit queue.
This would come with the downside of not having builders available at the gloas fork boundary.
```

</details>

---

## 2026-05-07 (epbs)

The discussion centered around implementing a pending builder deposits queue to handle potential DoS attacks, but significant concerns were raised about its complexity and timing. Potuz strongly opposed the queue implementation, citing it as a large change during devnet stages that could introduce new consensus bugs, particularly race conditions between builder and validator pubkeys being inserted simultaneously. The main worry was that deposit handling has historically been the source of most consensus bugs in the spec.

As an alternative solution, jtraglia created a prototype PR for a separate pending deposits queue for builders, but after review revealed bugs and complexity issues, the PR was closed. The team discussed simpler alternatives like capping the number of deposits, using forkchoice to reorg problematic payloads, or requiring builders to have 32 ETH deposits. There was also debate about whether the attack (costing around $20M) was severe enough to warrant complex solutions, as it's expensive and doesn't affect chain liveness.

The discussion concluded with agreement to move forward knowing the issue exists, with plans to benchmark whether this is actually a viable attack vector. However, there was disagreement about increasing the SSZ limit, with concerns this could worsen the problem. Key action items include benchmarking client performance on deposit processing and determining if current limits can handle the load with simpler mitigation systems.

**Participants:** barnabasbusa, jtraglia, m.kalinin, potuz, tersec

<details>
<summary>Raw messages</summary>

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
```

</details>

