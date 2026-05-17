# Execution Requests Duplication and Network Efficiency

**Channel:** epbs | **Date:** 2026-04-15

## Summary

The discussion centers on concerns about execution request duplication in Ethereum's network protocol and its impact on efficiency. Raulvk identified that execution requests are transmitted twice - once in the execution payload and again in the beacon block - which could consume up to 1.5MiB of network capacity in worst-case scenarios, potentially negating the benefits of ePBS where beacon blocks should remain small for critical path optimization. They proposed a "stateful compression" mechanism where beacon blocks would only contain commitments to execution requests during gossip, with nodes expanding these commitments using data they already possess before feeding it into the State Transition Function (STF).

Potuz raised several technical objections to this optimization, particularly around checkpoint sync scenarios and the requirement that block validity should be a static check on the STF rather than depending on previous payload data. A key concern emerged around attestation safety - if only the proposer receives execution requests but attesters don't, the proposer could be grieved when the block gets reorged due to lack of network propagation. Potuz suggested that Payload Time Commitments (PTC) could potentially address this by verifying request correspondence, though this would break checkpoint sync functionality. The discussion also touched on whether optimistic sync specifications might be overly complex post-merge, with Gloas potentially allowing sync without payload retrieval.

The conversation ended without resolution, with several technical details requiring further clarification. Key open questions include the exact mechanics of checkpoint sync edge cases, the safety implications of independent EL/CL syncing, and whether the proposed compression mechanism can work within existing attestation and verification constraints. Jtraglia and Potuz agreed to continue the discussion privately to resolve disagreements about payload verification scenarios.

## Participants

- jtraglia
- nflaig
- potuz
- raulvk
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-15T17:19:38.381000+00:00] jtraglia: Deferred payload processing concerns
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
