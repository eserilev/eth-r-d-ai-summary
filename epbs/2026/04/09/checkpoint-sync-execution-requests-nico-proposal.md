# Checkpoint Sync with Execution Requests in Nico's Proposal

**Channel:** epbs | **Date:** 2026-04-09

## Summary

The discussion centers on nflaig's proposal for handling execution requests in checkpoint sync, where execution requests from the previous slot are embedded in child beacon blocks and applied during state transition. Nflaig argues this maintains correctness through the state_root and preserves pre-gloas checkpoint sync semantics, requiring only the finalized state plus next block. However, potuz raises significant concerns about validation complexity, pointing out that blocks still need to verify that embedded requests correspond to those in the previous payload, potentially still requiring access to historical payloads for validation.

The conversation reveals deeper implementation challenges around caching and state management. Potuz explains how the proposal would complicate current optimizations where clients cache head state immediately after block arrival and preemptively execute epoch transitions. The new approach would require invalidating and recomputing cached states for every slot advance when requests are applied, potentially destroying existing next-slot cache optimizations that are critical for performance.

Several technical questions remain unresolved, including whether the state transition function (STF) signature needs to change, how to validate blocks without access to previous payloads during checkpoint sync, and whether adding an `execution_requests_root` to bids could address some validation concerns. The discussion suggests this may be better handled at the API level rather than through protocol changes, though no clear consensus emerges on the best path forward.

## Participants

- nflaig
- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-09T15:50:23.627000+00:00] nflaig: in my proposal, the previous slot's executions requests are embedded in the child beacon block and applied during `state_transition`, so correctness should be guaranteed by the `state_root`, meaning for checkpoint sync only having the finalized state + next block should be sufficient (same as pre-gloas)
[2026-04-09T15:59:25.288000+00:00] potuz: you will send the requests in the child block in full? in which case you need to assert that they correspond to the requests that were in the payload before anyway
[2026-04-09T15:59:37.736000+00:00] potuz: and so you still need the payload to validate the block
[2026-04-09T15:59:44.131000+00:00] potuz: or am I missing something?
[2026-04-09T16:00:02.529000+00:00] potuz: if there is a commitment in the bid that can be solved
[2026-04-09T16:11:07.214000+00:00] potuz: So I have a few problems with the proposal, most importantly the fact that I think this should be dealt at the API level (eg relax checkpoint sync and change dependent roots to signal the payload). But even if we decide to go with this, I think it makes our caching more complicated.  We currently pick head as soon as the block/payload arrives and we cache that state that will be the head state to apply the next block immediately. With this approach we can do this right after the CL block. If it's slot 31 we can immediately execute epoch transition which is a big plus. But in general it is a problem because it makes slot advancing more complicated: For a regular block we would do this:

- Get the CL block, execute state transition.  Preemptively do epoch transition if it's slot 31. 
- Get the EL block. Execute the requests to cache the next state that will be used to apply the next block. 
- If there's no new block, then we need to remove that cached state with the requests applied, advance the slot on that and reapply the requests again. 

Every slot that passes we invalidate some state and recompute the cached head to be able to apply a block. 

Today the situation is much simpler: any object is missing we just do nothing or we just advance the block to the next slot. 

Our next slot cache is probably the single piece of most complicated code for block processing, and this will destroy it. I'm a bit scared of such a change. Perhaps the simplest approach is to *not* cache the requests applied, but this feels incredibly suboptimal to me
[2026-04-09T16:20:42.406000+00:00] nflaig: during checkpoint sync, you would fetch the finalized `anchor_state` and `anchor_block`, and the state here is the post `state_transition` state of the anchor block, as a node you can run `state_transition` on the anchor block and should produce the same `state_root` as the `anchor_state`, if the previous payload was not valid, then validators would not vote for that state at all so it wouldn't be finalized in the first place
[2026-04-09T16:21:47.729000+00:00] potuz: how do you jknow that the anchor block has the right requests?
[2026-04-09T16:22:15.779000+00:00] nflaig: if it doesn't it would produce a different `state_root`
[2026-04-09T16:22:30.955000+00:00] potuz: the block has a post-state root not a pre-state root
[2026-04-09T16:22:38.587000+00:00] potuz: or now blocks will have two state roots?
[2026-04-09T16:27:30.114000+00:00] nflaig: I don't see why it's needed `assert anchor_block.state_root == hash_tree_root(anchor_state)` isn't that the only condition we need to verify when bootstrapping from a finalized checkpoint state?
[2026-04-09T16:31:05.961000+00:00] potuz: If the justified checkpoint is say block 31, you will get the post state of 31 advanced to 32. And the first block you need to apply is 33 that is based on 31. The state of 31 does not have the requests of its payload (it was full) so the root does not have this. The block 33 includes these requests, that need to be validated against the payload of 31. You can't do this without its payload.
[2026-04-09T16:53:16.936000+00:00] tbenr: > Get the EL block. Execute the requests to cache the next state that will be used to apply the next block.

why would you execute the requests as the EL block arrives, instead of waiting next beacon block?
[2026-04-09T17:09:29.736000+00:00] nflaig: maybe this is a implementation details how clients checkpoint sync, the simplest approach is how Lighthouse does it and I believe this should be the same post-gloas under my proposal
- 1. `GET /eth/v2/debug/beacon/states/finalized` --> `anchor_state`
- 2. `GET /eth/v2/beacon/blocks/{state.latest_block_header.slot}` --> `anchor_block`
- 3.  `get_forkchoice_store(anchor_state, anchor_block)`

a equivalent for step 2. would be `GET /eth/v2/beacon/blocks/finalized`, maybe LH does this to avoid a race condition? but either way you have a finalized state and a finalized block, I don't see why you require the previous payload
[2026-04-09T17:11:35.440000+00:00] nflaig: client might not do step 2. and request the block from the network (lodestar for example) in which case you might need to fetch the previous payload but even that is fine, as the main point is to keep pre-gloas checkpoint state semantics (state advanced to the epoch boundary)
[2026-04-09T17:54:10.845000+00:00] nflaig: was talking with <@534934855113506836> about this, we can add the `execution_requests_root` to the bid, also if data duplication is a concern, I think we don't even need `execution_requests` in the `ExecutionPayloadEnvelope` anymore but that would require `engine_newPayload` to return them instead
[2026-04-09T18:12:39.246000+00:00] potuz: Why do you execute slot transition before the block arrives? Same reason
[2026-04-09T18:16:08.219000+00:00] potuz: You have this, so at this point you have the post state of block 31 in your root of your store, and you have the block for 31, but you do not have the paylaod for 31, so the first block you request is 33 (32 was missed) how do you validate 33 without having the payload for 31?
[2026-04-09T18:17:24.262000+00:00] potuz: I do not understand at all how do you avoid the change in the STF signature
[2026-04-09T18:17:51.498000+00:00] potuz: And in fact I believe it to be impossible to do
[2026-04-09T20:28:10.215000+00:00] tbenr: but if i understand correctly that execution request execution wont ever trigger an epoch transition, which is the heavy thing. It will be just as today, in which we process them with the block and we don't have any special care (the only thing that changes is that we execute the requests from the previous block). Maybe i'm missing something
```

</details>
