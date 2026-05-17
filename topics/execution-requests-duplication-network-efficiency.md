# Execution requests duplication and network efficiency

---

## 2026-04-15 (epbs)

This discussion centers on the inefficiency of duplicating execution requests in both execution payloads and beacon blocks, which could consume up to 1.5MiB of network capacity in worst-case scenarios. raulvk argues this duplication is wasteful and proposes a "stateful compression mechanism" where beacon blocks would contain only commitments to execution requests during the critical path, with nodes expanding these commitments using data they already possess from previous payload execution. However, potuz raises significant concerns about this approach, particularly around checkpoint sync scenarios where nodes need to sync from finalized states without having access to previous payloads.

The core technical challenge involves maintaining the State Transition Function (STF) as a pure function of prestate and block, rather than introducing dependencies on external data. potuz explains that removing execution request duplication would break checkpoint sync because nodes couldn't validate new blocks without first fetching and processing previous payloads containing the necessary execution requests. Additionally, there are consensus safety concerns around attestation behavior - if only the proposer receives execution requests but attesters don't, it could lead to griefing attacks and chain reorganizations.

The discussion reveals fundamental tensions between network efficiency and protocol safety, with no clear resolution reached. Key open questions include whether the Payload Timeliness Committee (PTC) could provide sufficient guarantees for request availability, how to maintain checkpoint sync functionality, and whether optimistic sync mechanisms could be simplified or eliminated entirely. The conversation ends with plans for further discussion, indicating this remains an active area of investigation for Ethereum's execution request architecture.

**Participants:** jtraglia, nflaig, potuz, raulvk, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-15T17:35:40.880000+00:00] raulvk: this investigation sent me down a rabbit hole peeling off layer after layer on execution request handling. i have to say, this is probably one of the most non-sensical parts of Ethereum client architecture. there is Solidity ABI parsing, SSZ conversion (in the EL!), Engine API JSON framing. and indirection, oh, so much indirection.
[2026-04-15T17:37:26.389000+00:00] potuz: wait but this question should have been rather trivial at least fow what I would have wanted to see: that is, what is the maximum SSZ length that requests can have, as a fraction of the  *average* beacon block
[2026-04-15T17:37:50.883000+00:00] potuz: because this is really the hurt that a malicious builder can do **for free**
[2026-04-15T18:04:30.656000+00:00] raulvk: i am trying to understand why we **need** to propagate the same data twice on the wire, it feels extremely wasteful. it'll be taking away 1.5MiB of capacity (poorly compressible worst case scenario) from the critical path, basically annulling one of the most useful benefits of ePBS: the beacon block is tiny and sufficient to start critical path work.
[2026-04-15T18:06:29.120000+00:00] raulvk: IUC, a necessary precondition to validate beacon block N+1 is that we must've received and executed the payload for N, which already includes the execution requests. so we already have them. it makes zero sense to duplicate them on the wire.
[2026-04-15T18:06:54.632000+00:00] raulvk: i must be misunderstanding something, please correct me!
[2026-04-15T18:10:15.486000+00:00] potuz: because validity of the block should be a static check on the STF, if we do not send this with the block the STF becomes a function of the previous payload, the block and the previous state
[2026-04-15T18:10:50.185000+00:00] raulvk: sure, but we can just virtualize this field
[2026-04-15T18:11:27.164000+00:00] raulvk: we don't need to send the data twice on the network. the beacon block can commit to it, and the beacon node can replace that commitment with the actual data it already has _before_ feeding it into the STF
[2026-04-15T18:11:30.378000+00:00] potuz: On the practical side of things also it is because without sending them twice then it would break the exact same problem that this PR is trying to solve: that you can request the finalized checkpoint from the node and you only need the next block to start proceeding, you do not need to start requesting payloads from previous epoch to be able to sync the next block
[2026-04-15T18:18:14.713000+00:00] raulvk: <@755590043632140352> to be clear: the network payload does not need to coincide 100% with the consensus payload. it's useful to view it as a stateful compression mechanism. during the critical path:
- we compress the data into a commitment in the beacon block N+1 for sending
- we expand into the full exec requests before feeding the expanded block into STF

when serving it via req/resp (for the sync from finalized checkpoint you just mentioned), the responder can also send the expanded response. it just doesn't make sense to send the same data twice in the critical path. especially given the WCS is 1.5MiB.
[2026-04-15T18:41:40.053000+00:00] nflaig: > payloads from previous epoch to be able to sync the next block
that shouldn't be needed even if we remove `parent_execution_requests` as the `anchor_state` already has the execution requests of the `anchor_block` applied, so you should be able to sync the next block
[2026-04-15T18:43:48.615000+00:00] potuz: I do not think that's true: finalized is slot 30 full, the state is the post state of the CL block advanced to 32, that's anchor state. You cannot apply 33 (the next block) without recovering the payload of 30, this is the main problem
[2026-04-15T18:45:07.879000+00:00] potuz: well, then explain me how you solve the checkpoint sync scenario I am pointing to
[2026-04-15T18:45:19.505000+00:00] potuz: which is what led to the uproar over the current status
[2026-04-15T19:00:52.982000+00:00] nflaig: ah I see what you mean but that's needed either way, you need the payload of 30 to process 33 since the payload is not finalized and need to be verified (ie. part of `store.payloads`) to pass this check
```
if is_parent_node_full(store, block):
    assert is_payload_verified(store, block.parent_root)
```
in `on_block`, only the `anchor_state.latest_block_hash` payload is finalized
[2026-04-15T19:03:22.468000+00:00] raulvk: 1. You fetch the finalized state from a peer.
2. You sync to HEAD via req/resp (BeaconBlocksByRoot/BeaconBlocksByRange).
3. The peer serves the expanded form, with the **full parent_execution_requests inlined**, not the compressed form.
[2026-04-15T19:04:37.458000+00:00] potuz: no, with 5094 you do not need the payload, you get the block 33 and you check against the bid in 30, that's the whole point of your PR as far as I can tell!
[2026-04-15T19:05:09.407000+00:00] potuz: this doesn't work, we sync to finalized state from the checkpoint server that gives us the finalized state already
[2026-04-15T19:05:37.944000+00:00] potuz: I cannot start syncing until I have seen the requests from **before** the finalized epoch start
[2026-04-15T19:11:11.429000+00:00] potuz: I've been saying this forever 🙂 https://discord.com/channels/595666850260713488/874767108809031740/1491830057327526138
[2026-04-15T19:11:16.923000+00:00] nflaig: that is true from a pure STF perspective but I thought doing [gloas range sync](https://www.potuz.net/posts/gloas-range-sync/) is only safe up until the finalized checkpoint
[2026-04-15T19:13:07.472000+00:00] potuz: So there's still an edge case that a payload with that given blockhash may not exist at all but in that case the incoming block would have to be invalid and you won't be able to sync that chain eventually
[2026-04-15T19:13:32.017000+00:00] potuz: I'm still awaiting for EL devs to chime in that thread as to the safety of syncing the EL and CL independently
[2026-04-15T19:16:03.370000+00:00] nflaig: I guess what you are saying is you can sync optimistically even to the head with 5094 without requiring fetching payloads?
[2026-04-15T19:16:31.571000+00:00] potuz: even with less assurances than optimistically: I think even checking the blockhash is unnecessary
[2026-04-15T19:17:02.328000+00:00] potuz: I am starting to suspect that we kept an overly complex optimistic sync spec that we could have ditched after the merge
[2026-04-15T19:17:11.383000+00:00] potuz: and Gloas allows us to sync without ever getting a payload at all
[2026-04-15T19:23:15.251000+00:00] raulvk: can you elaborate? what is the exact problem with the checkpoint sync service? are you worried about some off-by-1 sort of situation?
[2026-04-15T19:24:26.948000+00:00] raulvk: the snapshot would just include the beacon blocks in expanded form, no?
[2026-04-15T19:24:46.839000+00:00] potuz: And Raul no, you cannot do what you want because there's no attestations to the Payload. If the proposer got the requests but attesters didn't them the proposer would be grieved
[2026-04-15T19:25:45.239000+00:00] potuz: So you cannot avoid sending them with the block unless there's a round of attestations to DA of the Payload
[2026-04-15T19:26:42.541000+00:00] potuz: One could overload PTC for this but we need to check safety even in this case and again it breaks checkpoint sync again
[2026-04-15T19:27:44.891000+00:00] potuz: It's the situation I describe above with 30 finalized
[2026-04-15T19:29:27.063000+00:00] raulvk: sorry, i don't get this. you're saying that PTC does not actually provide a strong enough signal of the execution payload having been received by the whole network?
[2026-04-15T19:30:54.969000+00:00] potuz: It does but it doesn't do any checking
[2026-04-15T19:31:10.871000+00:00] potuz: So the requests in the Payload may not correspond to the commitment
[2026-04-15T19:31:16.941000+00:00] raulvk: for an attester to attest successfully to beacon block N+1, it must have received the execution payload of N _and_ executed it so it can verify that the state root matches, no?
[2026-04-15T19:31:27.910000+00:00] raulvk: otherwise it won't be able to attest to N+1 to begin with
[2026-04-15T19:31:43.422000+00:00] potuz: Yes
[2026-04-15T19:32:32.045000+00:00] raulvk: so if this is a precondition for attestation, then it follows that the execution requests _are_ available. otherwise we have a larger problem
[2026-04-15T19:34:27.417000+00:00] potuz: No, again: no one got the requests except the next proposer that got them. No one has imported the Payload except the proposer that got it and the proposer has built on full, but it's reorged cause no one has the requests
[2026-04-15T19:35:05.820000+00:00] potuz: If instead the proposer sends the requests then the Payload has to be present
[2026-04-15T19:35:37.829000+00:00] potuz: So the only way to do this on gossip is to also request that the PTC checks that the requests correspond to the bid
[2026-04-15T19:36:08.022000+00:00] potuz: And that will still break checkpoint sync but at least work for gossip if we put this extra trust on the PTC
[2026-04-15T19:37:48.318000+00:00] potuz: But we already have these sort of equivocation attacks by builders anyway
[2026-04-15T20:26:51.084000+00:00] jtraglia: How would this affect the next proposer? Wouldn't they just build on-top of a different block?
[2026-04-15T20:40:28.095000+00:00] nflaig: so it looks like even if you optimize deposits by batching them, you will need a 193M gas limit and lock up ~$20.5M in ETH to reach the ssz size limit, the current worst case size on mainnet is ~489 KB
[2026-04-15T21:49:56.351000+00:00] potuz: Not if they have everything. They think the Payload is good
[2026-04-15T21:50:22.293000+00:00] tbenr: If we violate the rule that stf is only a function of prestate and block, if it becomes a function of prestate, data and block, then we could blind them in the block (so in beacon block we will have previous execution requests root and current bid) and everything should remain the same. We are essentially putting the whole network in the condition in which only the proposer is now ("to build on full you have to know the full payload" becomes "to import the block you have to know the previous full payload"). 
The stf will check that you are passing execution requests that correctly unblind the root present in the block.

But that's a big departure. A stf depending on offchain data is huge.

You may say it isn't really off chain, it is on another chain:)
[2026-04-15T21:51:21.492000+00:00] jtraglia: Why would they think the payload is good? `verify_execution_payload_envelope` will throw an exception.
[2026-04-15T21:58:37.949000+00:00] tbenr: And optimistic sync will be gone forever
[2026-04-15T22:08:06.806000+00:00] potuz: Not if they have everything
[2026-04-15T22:08:41.757000+00:00] potuz: Already drinking with a friend, hit me later on DM and we'll go over this 🙂
[2026-04-15T22:09:01.394000+00:00] jtraglia: Okay lol. Will message you tomorrow.
[2026-04-15T22:09:09.645000+00:00] jtraglia: But I don't think this makes sense 😅
```

</details>

---

## 2026-04-16 (epbs)

The discussion centers on optimizing execution request propagation in Ethereum's network architecture to avoid redundant data transmission. Participants distinguish between two network modes: live syncing (propagation on critical path) where execution requests don't need separate propagation since nodes already have the parent payload, and catching up (non-critical) where requests are obtained via checkpoint sync and req/resp protocols. The key proposal is to use "execution-request-blinded" data structures on the gossip layer while maintaining unblinded versions on the RPC layer, potentially avoiding double propagation without requiring a hard fork.

Technical challenges emerged around maintaining the state transition function (STF) integrity and RPC complexity. While execution requests could be reconstructed by the execution layer from payload transactions, this would complicate the engine API and require additional validation steps. The discussion reveals that execution requests were originally designed to be part of the payload itself, but the current dual-list approach requiring synchronization validation adds complexity. Removing requests from the `ExecutionPayloadEnvelope` could resolve payload equivocation issues, though they would still be needed in beacon blocks via `parent_execution_requests` for the STF.

The conversation concludes that while technical solutions exist to make the STF dependent on request availability, the trade-offs involve increased RPC serving complexity and potential changes to the engine API. The main question remains whether the networking optimization benefits justify these implementation complexities, particularly since the primary concern is consensus layer gossip rather than execution layer gossip overhead.

**Participants:** nflaig, potuz, raulvk, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-16T11:05:05.883000+00:00] potuz: Yes this is correct, in fact they were added to the block just to prevent this signature change
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

