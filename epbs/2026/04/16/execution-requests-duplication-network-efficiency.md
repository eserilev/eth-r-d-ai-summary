# Execution requests propagation and networking optimization

**Channel:** epbs | **Date:** 2026-04-16

## Summary

The discussion focuses on optimizing execution requests propagation in Ethereum's networking layer to avoid redundant data transmission. The key insight is distinguishing between live syncing (where execution requests are already available from the parent block execution) and catching up (where requests are needed via checkpoint sync or req/resp protocols). The proposal suggests removing execution requests from gossip propagation while keeping them in formal beacon blocks, potentially using "execution-request-blinded" data structures on the gossip layer while maintaining unblinded versions on the RPC layer.

The conversation explores technical implementation details, including modifying the engine API to have `engine_newPayload` return execution requests for validation, removing requests from `ExecutionPayloadEnvelope` since they're reconstructible from the payload, and the implications for state transition functions (STF). Participants note this approach could prevent payload equivocation issues related to execution requests and could potentially be delivered without requiring a hard fork through coordinated client releases.

The main open question centers on whether the complexity trade-offs are worthwhile, particularly regarding RPC serving complications where range serving would need to "unblind" blocks by requesting execution requests from the database. While technically feasible, the discussion doesn't reach a clear consensus on whether to pursue this optimization given that the primary performance concern is in consensus layer gossip rather than execution layer gossip.

## Participants

- nflaig
- potuz
- raulvk
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-16T12:25:23.196000+00:00] raulvk: > "to build on full you have to know the full payload" becomes "to import the block you have to know the previous full payload"

we should distinguish between two modes: syncing and catching up, because these are two independent network planes and must be treated differently.

- live syncing == propagation == critical path. to import beacon block N (head) you need to have executed its parent. this means you already have the execution payload and therefore the requests. no need to propagate them again.
- catching up == no propagation (or rather: you use propagation at an informational level only). here you rely on checkpoint sync to a finalized epoch, then req/resp onwards. both the snapshot and the req/resp contain the execution requests.

there is a third mode (reorging), which is is really (1) falling back to (2) temporarily. in all cases, when you **ask** for it, you are served the requests. all i'm saying is that we should avoid *propagating* them twice. they can still be part of the formal beacon block, they are just elided in the gossip network payload.
[2026-04-16T12:41:16.210000+00:00] tbenr: I agree with you that the definition of what is "on-chain" must not necessarily be equivalent of what is on gossip layer.
Ignoring the technical consequences (can differ from client to client), we could have a "execution-request-blinded" datastructure running on gossip, while having the unblinded version on RPC layer.
[2026-04-16T12:43:43.969000+00:00] tbenr: block+enevlop gossip will give you everything.

another option could be to have the RPC datastructure blinded too, but havng an RPC dedcated to ExecutionRequestByRoot (beacon block root)
[2026-04-16T12:45:25.629000+00:00] tbenr: so the on-chain consensus remains exactly as is, what changes is the networking layer supporting those structures.
It could be even delivered as a coordinated release not requiring an HF
[2026-04-16T12:50:51.079000+00:00] nflaig: > all i'm saying is that we should avoid propagating them twice.
I don't think it's technically needed to have them in the `ExecutionPayloadEnvelope`, the local EL can always reconstruct them from the payload but that would require `engine_newPayload` to return them and we lose the ability to statically check them on gossip/fork choice before calling the EL
[2026-04-16T13:04:33.977000+00:00] potuz: Yeah with 5094 we could do this at the cost of slightly complicating the engine API, we would pass the bid root on new Payload and it will add a validation step on the EL to check that they match. The next proposer will also require an endpoint to recover them. I think this is not worth it cause the pain is in CL gossip not EL gossip
[2026-04-16T13:27:58.018000+00:00] potuz: <@795439202732867594> <@504202741933932544> we clearly have solutions to make the STF dependent on the availability of the requests, however, in addition of making the STF signature be clearly distinct for the first time, it would make RPC serving much more complicated. Range serving of blocks should unblind the blocks by requesting the requests from DB, assembling the block and sending them. 

This is all doable, the question is if we want to go this route.
[2026-04-16T13:32:25.884000+00:00] nflaig: kinda related, I was curious why you mentioned including a ton of invalid requests is free for a builder, because they have to pass `verify_execution_payload_envelope` otherwise the payload is invalid and the next proposer will not build on it, there is comment below that says "deposit requests that are crap", in what regard?
[2026-04-16T13:37:58.358000+00:00] potuz: yeah, I'm actually disregarding EL validations, we do send the requests to the EL to verify them. My worry is mostly about the broadcast cost and situations of payload equivocations with bad requests.
[2026-04-16T13:39:17.125000+00:00] potuz: perhaps the garbage requests is not a problem if the cost to include them and passing validation is more than the expected profit of a reorg, which should probably be fine since a reorg cannot steal a payload in ePBS.
[2026-04-16T13:41:28.138000+00:00] nflaig: to pass validation you need to lock up a huge amount of capital and currently the max size is limited by the gas limit
[2026-04-16T13:41:48.428000+00:00] nflaig: but I brought this up because removing `execution_requests` from `ExecutionPayloadEnvelope` kinda resolves this issue?
[2026-04-16T13:42:27.680000+00:00] nflaig: can't you just let the EL return it in the `engine_newPayload` and do the root check on the CL side?
[2026-04-16T13:51:45.650000+00:00] potuz: you mean the requests would be part of the payload itself instead of the envelope?
[2026-04-16T13:52:58.723000+00:00] nflaig: they are part of the payload already (sort of) but as transactions, the EL can always reconstruct them
[2026-04-16T13:56:08.716000+00:00] potuz: yes so this was originally the design, for requests, I do not know why we went with the two seaparate lists that need to be validated that they are in sync
[2026-04-16T13:58:01.301000+00:00] nflaig: aren't they part of the CL block because pre-gloas you need them for stf? if you rely on them being given to you by the EL you can't run `state_transition` on a blinded block, that's at least one issue I see (also same reasoning as above that stf should be self-contained)
[2026-04-16T13:58:14.789000+00:00] potuz: removing the requests from the envelope also prevent payload equivocation at least with regards to these requests.
[2026-04-16T13:59:41.648000+00:00] potuz: well, this same issue would continue happening if we remove them from the payload, they are always needed for the STF. We can remove it from the payload and then add them to the block. We could remove them from both but again this breaks syncing without the payloads.
[2026-04-16T14:02:16.722000+00:00] nflaig: we do need them in the `BeaconBlock` via `parent_execution_requests` but I don't see a reason why the `ExecutionPayloadEnvelope` should have `execution_requests` since there is no stf anymore
[2026-04-16T14:03:10.189000+00:00] potuz: yeah currently they aren't needed except to make sure that they correspond to the committed root
```

</details>
