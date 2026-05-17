# Implementation Complexity and Caching Architecture Changes

---

## 2026-04-09 (epbs)

The discussion centers on the implementation complexity of a proposed caching architecture change for Ethereum clients. While wemeetagain notes the change fits well with Lodestar's architecture, potuz raises significant concerns about increased complexity for their implementation. The core technical change involves reordering epoch processing and payload processing, plus removing the state_root from the envelope, but potuz argues this fundamentally complicates their range syncing and caching strategies.

Potuz's main concern is with their "next slot cache" - currently they can cache the head state immediately after a block/payload arrives and preemptively execute epoch transitions at slot 31. The proposed change would require executing requests when the EL block arrives to cache the next state, but this creates complications when slots advance without new blocks, forcing them to invalidate and recompute cached states repeatedly. This would significantly complicate their most complex piece of block processing code.

The discussion includes a debate between potuz and tbenr about when to execute requests - whether upon EL block arrival (for caching optimization) or waiting for the next beacon block. Tbenr suggests the execution request processing won't trigger heavy epoch transitions like current slot transitions do, but the conversation ends with potential misunderstanding about the implementation details, leaving the complexity concerns unresolved.

**Participants:** potuz, tbenr, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-04-09T15:34:08.726000+00:00] wemeetagain: (this fits nicely into lodestar's caching architecture)
[2026-04-09T15:36:44.264000+00:00] potuz: yeah that's what I am not sure about, I think implementing this approach would be actually more complex than the current approach for us.  Right now it is simpler to reason about when to perform a state transition, and when to preemtively cache epoch transition. This probably is not that big of a change in this regard, things we do post-payload would be moved to post-block, but range syncing would be fundamentally different with this approach for us at least
[2026-04-09T15:37:11.035000+00:00] potuz: technically the only difference is in a single change of ordering between epoch processing and the last payload processing
[2026-04-09T15:37:31.756000+00:00] potuz: that's the whole content of the change (besides having to remove the state_root from the envelope)
[2026-04-09T16:11:07.214000+00:00] potuz: So I have a few problems with the proposal, most importantly the fact that I think this should be dealt at the API level (eg relax checkpoint sync and change dependent roots to signal the payload). But even if we decide to go with this, I think it makes our caching more complicated.  We currently pick head as soon as the block/payload arrives and we cache that state that will be the head state to apply the next block immediately. With this approach we can do this right after the CL block. If it's slot 31 we can immediately execute epoch transition which is a big plus. But in general it is a problem because it makes slot advancing more complicated: For a regular block we would do this:

- Get the CL block, execute state transition.  Preemptively do epoch transition if it's slot 31. 
- Get the EL block. Execute the requests to cache the next state that will be used to apply the next block. 
- If there's no new block, then we need to remove that cached state with the requests applied, advance the slot on that and reapply the requests again. 

Every slot that passes we invalidate some state and recompute the cached head to be able to apply a block. 

Today the situation is much simpler: any object is missing we just do nothing or we just advance the block to the next slot. 

Our next slot cache is probably the single piece of most complicated code for block processing, and this will destroy it. I'm a bit scared of such a change. Perhaps the simplest approach is to *not* cache the requests applied, but this feels incredibly suboptimal to me
[2026-04-09T16:53:16.936000+00:00] tbenr: > Get the EL block. Execute the requests to cache the next state that will be used to apply the next block.

why would you execute the requests as the EL block arrives, instead of waiting next beacon block?
[2026-04-09T18:12:39.246000+00:00] potuz: Why do you execute slot transition before the block arrives? Same reason
[2026-04-09T20:28:10.215000+00:00] tbenr: but if i understand correctly that execution request execution wont ever trigger an epoch transition, which is the heavy thing. It will be just as today, in which we process them with the block and we don't have any special care (the only thing that changes is that we execute the requests from the previous block). Maybe i'm missing something
```

</details>

