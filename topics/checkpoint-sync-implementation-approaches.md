# Checkpoint sync implementation approaches

---

## 2026-03-30 (epbs)

Stefan asked about implementation approaches for checkpoint sync, specifically whether teams request envelopes by root from peers or use checkpoint sync URLs with the Beacon API (noting that not all providers may support the API approach).

Potuz responded that their implementation uses a "request by range" approach, where they request payloads from a specific slot and blocks from slot+1. This appears to be a different strategy than the two options Stefan initially mentioned, suggesting there may be multiple viable approaches to implementing checkpoint sync functionality.

**Participants:** potuz, stefanbratanov

<details>
<summary>Raw messages</summary>

```
[2026-03-30T16:10:39.629000+00:00] stefanbratanov: <@755590043632140352> how did you guys implement checkpoint sync, do you ask a peer for the envelope by root or use the checkpoint sync url and query the Beacon API (which I presume not every provider would support that)
[2026-03-30T16:13:22.346000+00:00] potuz: we request by range, request payloads from slot and blocks from slot+1
```

</details>

---

## 2026-04-01 (epbs)

The devnet has successfully finalized, but there are issues with Lodestar's checkpoint synchronization when attempting to sync from a Prysm node. The checkpoint sync is failing with a "no finality known" error (status 500) when trying to fetch the weak subjectivity state from the checkpoint sync URL.

barnabasbusa identified this as likely being a checkpointz bug and began investigating. Additionally, Lodestar appears unable to perform forward synchronization from a finalized checkpoint, with logs showing "Block not found for id 'head'" errors when attempting to get block headers.

nflaig has taken on investigating the Lodestar synchronization issues. The main action items are resolving the checkpointz bug affecting checkpoint sync and fixing Lodestar's forward sync capability from finalized checkpoints.

**Participants:** barnabasbusa, nflaig

<details>
<summary>Raw messages</summary>

```
[2026-04-01T10:08:19.710000+00:00] barnabasbusa: we just finalized the devnet
[2026-04-01T10:11:54.422000+00:00] barnabasbusa: Lodestar doesn't like checkpoint syncing off of prysm ```
Apr-01 10:11:19.882[]                 info: Fetching checkpoint state checkpointSyncUrl=https://checkpoint-sync.epbs-devnet-1.ethpandaops.io
 ✖ Error: Unable to fetch weak subjectivity state: getStateV2 failed with status 500: no finality known
    at fetchWeakSubjectivityState (file:///usr/app/packages/cli/src/networks/index.ts:211:11)
    at processTicksAndRejections (node:internal/process/task_queues:104:5)
    at fetchWSStateFromBeaconApi (file:///usr/app/packages/cli/src/cmds/beacon/initBeaconState.ts:399:49)
    at initBeaconState (file:///usr/app/packages/cli/src/cmds/beacon/initBeaconState.ts:214:24)
    at Object.beaconHandler [as handler] (file:///usr/app/packages/cli/src/cmds/beacon/handler.ts:75:54)
```
[2026-04-01T10:12:52.157000+00:00] barnabasbusa: seems to be a checkpointz bug, gonna look into it
[2026-04-01T10:18:29.299000+00:00] barnabasbusa: <@586161934425128960>`Apr-01 10:17:49.511[rest]             warn: Req req-5l getBlockHeader failed reason=Block not found for id 'head'`  looks like lodestar is still unable to forward sync from a finalized checkpoint
[2026-04-01T11:24:28.815000+00:00] nflaig: looking into that
```

</details>

---

## 2026-04-03 (epbs)

The discussion centers on how to handle finalized checkpoint states in Gloas when block slots are skipped, particularly when the checkpoint slot (n % 32 == 0) has no block. Tuyen4818 argues that the finalized state should be the post-envelope state of slot n-1 dialed to slot n, maintaining backward compatibility where finalized states always have slots respective to checkpoint epochs (slot % 32 == 0). This approach ensures nodes can properly sync from finalized states and maintains consistency with pre-Gloas behavior where finalized states correspond to checkpoint epochs.

The debate involves whether both FULL and EMPTY payload variants of slot n-1 could be considered valid for finalization, with 0xunclebill questioning whether post-envelope states can truly be considered finalized with the same certainty as pre-Gloas. Terencechain suggests that attestations can signal empty or full variants via committee index, supporting the idea of advancing to the epoch boundary. For fork choice updates (FCU), the implementation passes either parent_block_hash (for EMPTY variants) or block_hash (for FULL variants) as the finalized block hash.

Potuz provides a definitive conclusion that "the finalized state is always post CL block, never post envelope," though acknowledging that finalization could theoretically signal full or empty based on descendant attestations, but this tracking isn't currently implemented. This leaves some tension between implementation approaches and the theoretical framework for handling checkpoint states in skip slot scenarios.

**Participants:** 0xunclebill, potuz, terencechain, tuyen4818

<details>
<summary>Raw messages</summary>

```
[2026-04-03T03:44:12.890000+00:00] tuyen4818: I got an error in our e2e test, forkchoice picked EMPTY as head when preparing for next slot
the fix is to pull up the slot in that case by passing the next clock slot, to trigger `gloas` tie-breaker logic of `FULL vs EMPTY` https://github.com/ChainSafe/lodestar/pull/9164
instead of using store's currentSlot
found it's a bit tricky since it's not in the spec so posting it here
[2026-04-03T04:25:55.219000+00:00] tuyen4818: just for clarification
suppose we're on a chain where every block slot `n % 32` is missed, no payload is missed for other slots
the chain is justified and finalized
in this case, given a finalized checkpoint of `(n, root)` where `n % 32 == 0` and no block + envelope for that slot `n`
I think the finalized state is the post-envelope state of slot `n - 1` dialing to slot `n`, not the post-block state of slot `n - 1` because:
- the envelope of `n - 1` happens before `n`, so it's considered finalized. And block `n + 1` is built on this envelope `n - 1` (FULL variant) instead of block `n - 1` (EMPTY variant)
- when we download that finalized state, we should be able to sync from there so post block state of `n - 1` is the wrong one, should be the post-envelope state of `n - 1` dialing to `n`

so finalized state could also be post-envelope state passing through epoch-transition in the case the block at checkpoint slot is skipped
correct me if I'm wrong cc <@755590043632140352> <@491624610215886849>
[2026-04-03T05:40:54.048000+00:00] 0xunclebill: how do we know the full variant is the finalized one? assuming slot `n` is skipped, the only way we could know if `n-1` is the full variant is by looking at slot `n+1` which isnt finalized yet
[2026-04-03T05:41:39.726000+00:00] 0xunclebill: it seems like the finalized checkpoint state should always be the block state?
[2026-04-03T05:43:08.811000+00:00] 0xunclebill: and similarly for the justified state
[2026-04-03T06:14:55.786000+00:00] tuyen4818: when you have a new justified/finalized checkpoint from a block/postBlock state, you can either use `execution_payload_availability` field there, or the forkchoice view of that block to see if FULL variant of `n-1` is on the canonical chain
[2026-04-03T06:17:53.203000+00:00] tuyen4818: a checkpoint of `(n, root)` always have a true `n % 32 == 0`, same to checkpoint state
so it must be the postEnvelope state dialing to `n` to me, if envelope is on the canonical chain (based on the above conclusion)
both postBlock state and postEnvelope state of `n - 1` are not justified/finalized BeaconState
it should always dial to `n`
[2026-04-03T06:20:40.040000+00:00] tuyen4818: postBlock state of `(n - 1)` dialing to `n` cannot be the justified/finalized state in that case because we cannot use that state to sync
[2026-04-03T06:23:50.925000+00:00] 0xunclebill: we're talking about when slot n is skipped (and `n % 32 == 0`), correct? wouldn't both the full and empty payload variant of `n - 1 `be valid wrt to finalization? ofc we could check our view of the canonical chain, but it seems to me that the postBlock and postEnvelope state of `n-1` are technically valid
[2026-04-03T06:25:54.646000+00:00] 0xunclebill: if we broadcast the postEnvelope state as the finalized state any node consuming that data will reject any chain built on top of the empty state, even though its technically valid (i think?)
[2026-04-03T06:31:41.068000+00:00] tuyen4818: > we're talking about when slot n is skipped (and n % 32 == 0), correct?
yes
> wouldn't both the full and empty payload variant of n - 1 be valid wrt to finalization? ofc we could check our view of the canonical chain, but it seems to me that the postBlock and postEnvelope state of n-1 are technically valid
both of them are finalized, same to block/envelope state slot `n - 2`
and we should pick the last one that is finalized
that said postBlock state cannot be the checkpoint state because there is 1 state after it, and before `n` is finalized
so to me postEnvelope state dialing to `n` is the finalized state

this is important because:
- when a checkpoint is finalized, beacon node needs to persist the correct BeaconState
- when a node download a finalized state of (n, root) where `n % 32 == 0` it needs to be able to sync from there

in this case, `postBlock` state dialing to `n` cannot be synced, only `postEnvelope` state dialing to `n` can
[2026-04-03T06:46:42.304000+00:00] 0xunclebill: I don't see how the postEnvelope state can be finalized in this case. attestations would be voting for target block root `n - 1` . It seems to me that all we can safely say is that the postBlock state for `n-1` is finalized. the node consuming that data would have to then try syncing the rest of the chain to figure out if `n - 1` has a full or empty payload
[2026-04-03T07:05:07.512000+00:00] tuyen4818: my thinking is that a checkpoint of `slot, root` always has `slot % 32 == 0`, even through it's a skip slot
a finalized state is always a checkpoint state, so finalized state must have `state.slot == 0 ` as well
and that's correct up until fulu, `finalizedState.slot` has the slot respective to the checkpoint's epoch
I'd make it backward compatible starting from gloas, so finalized state should be postEnvelope state dialing to `n` in my view

> attestations would be voting for target block root n - 1
I agree this is also correct, but if we get `postBlock` state of `n-1` as the finalized state, then starting from gloas, it could happen that a finalized state has whatever slot, it's not necessarily has `slot % 32 == 0`, and especially the finalized state does not have the respective slot to the finalized checkpoint's epoch. This is not backward compatible.
[2026-04-03T07:09:50.250000+00:00] tuyen4818: this could also be implementation specific
in the case of lodestar, we always store checkpoint states, and all of them have `state.slot === 32`, and it's always respective to an epoch of checkpoint, the finalized state is always a checkpoint state
it should help us avoid an epoch transition for whatever reorg scenario
[2026-04-03T07:14:49.126000+00:00] terencechain: For skip slot, attestations do signal empty or full via committee index though, so i think post CL or EL state given attestations signal (or what you get from fork chice store) then advance up to the epoch is likely the correct thing to do here
[2026-04-03T07:16:59.448000+00:00] terencechain: another questions is what do people do for finalized / justified block hash to pass for FCU.. do you take the parent block hash or post CL block hash in skip slot scenario
[2026-04-03T07:18:46.212000+00:00] tuyen4818: agree with this
it means to me the finalize state always have `slot % 32 == 0`, and it's respective to the finalized checkpoint's epoch
[2026-04-03T07:28:58.265000+00:00] tuyen4818: in lodestar we track the finalized checkpoint along with `payloadStatus`, based on that we always have the exact ProtoBlock that's finalized and we pass `execution_payload_block_hash` of that ProtoBlock
and `execution_payload_block_hash` is `parent_block_hash` for EMPTY variant and `block_hash` for FULL variant

in short:
- if the finalized proto block is an `EMPTY` variant, we pass `parent_block_hash`
- if the finalized proto block is a `FULL` variant, we pass the `block_hash`

that's consistent to my view about the finalized checkpoint state above
[2026-04-03T07:32:34.693000+00:00] terencechain: so if there's no skip slot.. that it's slot 0, its empty right?
[2026-04-03T07:33:03.386000+00:00] tuyen4818: yes, and in that case we pass `parent_block_hash` as the finalized block hash to fcu
[2026-04-03T07:41:54.565000+00:00] 0xunclebill: yeah attestations can signal empty or full but thats just for following head not for FFG, right? It doesn't seem to me that we could ever safely say that the post envelope state is finalized in the example above
[2026-04-03T07:42:51.974000+00:00] 0xunclebill: well i guess we could have some degree of safety, but it wouldn't be 100% certain like what a finalized state means currently pre-gloas
[2026-04-03T07:48:20.876000+00:00] tuyen4818: right, we don't count on that to decide
but my idea is the same: finalized state always have slot respective to the finalized checkpoint
we can ignore everything before that finalized state. If a node has to restart, or another beacon-node requests that finalized state, we can start syncing from there, and it does range sync from the next epoch
[2026-04-03T10:50:23.250000+00:00] potuz: No, the finalized state is always post CL block, never post envelope
[2026-04-03T10:52:25.988000+00:00] potuz: You could always argue that finalization in fact does signal full or empty because the attestations for the descendents that contributed to finalization are based on full or empty. But we do not track this
```

</details>

---

## 2026-04-05 (epbs)

0xunclebill investigated checkpoint sync failures on EPBS devnet 1 and identified several issues. Initially suspecting stale finalized epochs, they discovered the real problem was related to custody column requirements - their node could only connect to one peer and was struggling to find peers that could serve its custody column needs. When attempting to set custody requirements to 0, the node got banned by other peers, leading them to connect directly to a supernode instead.

The investigation revealed that their current checkpoint sync implementation was spamming Prysm nodes with blocks by range requests, resulting in bans. A key missing piece appears to be that the checkpointz server needs to implement the `GET eth/v1/beacon/payload_envelopes/{block_id}` endpoint, which their checkpoint sync tries to fetch before beginning range sync. While they have an alternative checkpoint sync implementation that doesn't require this endpoint, 0xunclebill believes checkpointz should support it.

The session ended with the checkpointz server returning 500 errors, and nflaig expressing solidarity having faced similar issues recently. The discussion leaves open questions about implementing the missing API endpoint and resolving the underlying connectivity and rate limiting issues.

**Participants:** 0xunclebill, nflaig

<details>
<summary>Raw messages</summary>

```
[2026-04-05T06:35:08.889000+00:00] 0xunclebill: ok I think i see the reason why we cant checkpoint sync on epbs devnet 1 (at least one of the reasons)
[2026-04-05T06:37:16.175000+00:00] 0xunclebill: ~finalized epoch being served by checkpoint server is epoch 1069 but finalized epoch is 1072~
[2026-04-05T06:38:16.673000+00:00] 0xunclebill: peers refuse the handshake
`Apr 04 23:37:53.880 DEBUG Handshake Failure                             peer_id: 16Uiu2HAmEf7cqrMFSvwJn9U3zHuoiu4nd1E88i2MiBLDbVXPxGGT, reason: "Old finality out of range"`

EDIT: ah wait these are just super stale nodes we are trying to peer with, definitely not the issue
[2026-04-05T06:39:34.793000+00:00] nflaig: <@358120958726373381> let me know if you managed to do anything on devnet-1 I gave up here https://discord.com/channels/595666850260713488/874767108809031740/1489590748008415292, back to reviewing code 😄
[2026-04-05T06:44:48.490000+00:00] 0xunclebill: ok our node is waiting to find peers that can serve its custody column requirements
[2026-04-05T06:45:27.222000+00:00] 0xunclebill: its only been able to connect to one peer. are we running any super nodes?
[2026-04-05T06:45:41.333000+00:00] 0xunclebill: im gunna try setting custody req to 0 and see what happens

haah okay everyone bans me when i do that, will connect to the supernode directly instead
[2026-04-05T06:49:04.413000+00:00] nflaig: should be https://dora.epbs-devnet-1.ethpandaops.io/clients/consensus#name=prysm-geth-3
[2026-04-05T08:11:08.250000+00:00] 0xunclebill: in our case we are just spamming prysm with blocks by range requests and getting banned. something funky on our end
[2026-04-05T08:23:31.077000+00:00] 0xunclebill: I think we need checkpointz to implement `GET eth/v1/beacon/payload_envelopes/{block_id}`
[2026-04-05T08:23:59.309000+00:00] 0xunclebill: at least our current checkpoint sync impl tries to fetch the envelope for the checkpoint slot before begining range sync
[2026-04-05T08:25:20.052000+00:00] 0xunclebill: we have another checkpoint sync impl that doesnt require checkpointz server to serve envelopes. but i feel like checkpointz server should implement that endpoint
[2026-04-05T09:10:55.539000+00:00] 0xunclebill: now checkpointz server is returning 500's
[2026-04-05T09:11:25.218000+00:00] nflaig: I feel your pain, was in the same spot 2 days ago 😄
```

</details>

---

## 2026-04-06 (epbs)

0xunclebill successfully synced a Prysm node and attributes the success to fortunate timing during the checkpoint sync process. They believe they "got lucky" by performing the checkpoint sync during an epoch that had no skipped boundary slots, which appears to be a critical factor for successful synchronization.

The discussion suggests there may be ongoing issues with Prysm node syncing related to skipped boundary slots during checkpoint synchronization, though the specific nature of these issues isn't detailed in the messages. No formal decisions or action items were discussed, and the technical details about why skipped boundary slots cause sync problems remain unexplored.

**Participants:** 0xunclebill

<details>
<summary>Raw messages</summary>

```
[2026-04-06T00:41:10.228000+00:00] 0xunclebill: Hmm I was able to sync a Prysm node yesterday
[2026-04-06T00:57:37.175000+00:00] 0xunclebill: I think I prolly got lucky and checkpoint synced during an epoch with no skipped boundary slot
[2026-04-06T07:42:05.118000+00:00] 0xunclebill: we synced!
```

</details>

---

## 2026-04-09 (epbs)

This discussion centers on nflaig's proposal to embed execution requests from the previous slot into child beacon blocks, with the goal of maintaining pre-Gloas checkpoint sync semantics where only a finalized state and next block are needed. The proposal suggests that correctness would be guaranteed by the `state_root`, allowing nodes to validate blocks during checkpoint sync without requiring additional payload data.

Potuz raises a critical validation concern: when a node checkpoint syncs and receives a finalized block (e.g., block 31) with its post-state, but then needs to process a subsequent block (e.g., block 33 after a missed slot), it cannot validate the execution requests embedded in block 33 without access to the payload from block 31. This creates a dependency that seemingly contradicts nflaig's claim that the previous payload isn't needed. The discussion explores potential solutions including adding an `execution_requests_root` to bids and possibly removing execution requests from the `ExecutionPayloadEnvelope` in favor of having `engine_newPayload` return them.

The conversation ends with Potuz expressing strong skepticism, stating he doesn't understand how the proposal avoids changing the state transition function (STF) signature and believes it may be "impossible to do." The core tension remains unresolved: how to validate execution requests during checkpoint sync without requiring access to previous payloads, which would maintain the desired simplicity of the current checkpoint sync process.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-09T15:50:23.627000+00:00] nflaig: in my proposal, the previous slot's executions requests are embedded in the child beacon block and applied during `state_transition`, so correctness should be guaranteed by the `state_root`, meaning for checkpoint sync only having the finalized state + next block should be sufficient (same as pre-gloas)
[2026-04-09T15:59:25.288000+00:00] potuz: you will send the requests in the child block in full? in which case you need to assert that they correspond to the requests that were in the payload before anyway
[2026-04-09T15:59:37.736000+00:00] potuz: and so you still need the payload to validate the block
[2026-04-09T15:59:44.131000+00:00] potuz: or am I missing something?
[2026-04-09T16:00:02.529000+00:00] potuz: if there is a commitment in the bid that can be solved
[2026-04-09T16:20:42.406000+00:00] nflaig: during checkpoint sync, you would fetch the finalized `anchor_state` and `anchor_block`, and the state here is the post `state_transition` state of the anchor block, as a node you can run `state_transition` on the anchor block and should produce the same `state_root` as the `anchor_state`, if the previous payload was not valid, then validators would not vote for that state at all so it wouldn't be finalized in the first place
[2026-04-09T16:21:47.729000+00:00] potuz: how do you jknow that the anchor block has the right requests?
[2026-04-09T16:22:15.779000+00:00] nflaig: if it doesn't it would produce a different `state_root`
[2026-04-09T16:22:30.955000+00:00] potuz: the block has a post-state root not a pre-state root
[2026-04-09T16:22:38.587000+00:00] potuz: or now blocks will have two state roots?
[2026-04-09T16:27:30.114000+00:00] nflaig: I don't see why it's needed `assert anchor_block.state_root == hash_tree_root(anchor_state)` isn't that the only condition we need to verify when bootstrapping from a finalized checkpoint state?
[2026-04-09T16:31:05.961000+00:00] potuz: If the justified checkpoint is say block 31, you will get the post state of 31 advanced to 32. And the first block you need to apply is 33 that is based on 31. The state of 31 does not have the requests of its payload (it was full) so the root does not have this. The block 33 includes these requests, that need to be validated against the payload of 31. You can't do this without its payload.
[2026-04-09T17:09:29.736000+00:00] nflaig: maybe this is a implementation details how clients checkpoint sync, the simplest approach is how Lighthouse does it and I believe this should be the same post-gloas under my proposal
- 1. `GET /eth/v2/debug/beacon/states/finalized` --> `anchor_state`
- 2. `GET /eth/v2/beacon/blocks/{state.latest_block_header.slot}` --> `anchor_block`
- 3.  `get_forkchoice_store(anchor_state, anchor_block)`

a equivalent for step 2. would be `GET /eth/v2/beacon/blocks/finalized`, maybe LH does this to avoid a race condition? but either way you have a finalized state and a finalized block, I don't see why you require the previous payload
[2026-04-09T17:11:35.440000+00:00] nflaig: client might not do step 2. and request the block from the network (lodestar for example) in which case you might need to fetch the previous payload but even that is fine, as the main point is to keep pre-gloas checkpoint state semantics (state advanced to the epoch boundary)
[2026-04-09T17:54:10.845000+00:00] nflaig: was talking with <@534934855113506836> about this, we can add the `execution_requests_root` to the bid, also if data duplication is a concern, I think we don't even need `execution_requests` in the `ExecutionPayloadEnvelope` anymore but that would require `engine_newPayload` to return them instead
[2026-04-09T18:16:08.219000+00:00] potuz: You have this, so at this point you have the post state of block 31 in your root of your store, and you have the block for 31, but you do not have the paylaod for 31, so the first block you request is 33 (32 was missed) how do you validate 33 without having the payload for 31?
[2026-04-09T18:17:24.262000+00:00] potuz: I do not understand at all how do you avoid the change in the STF signature
[2026-04-09T18:17:51.498000+00:00] potuz: And in fact I believe it to be impossible to do
```

</details>

---

## 2026-04-29 (epbs)

The discussion centers on why `execution_requests_root` is included in the bid structure. Potuz explains that it allows validation that requests in a block match those committed in the payload without needing access to the actual payload data. This design enables the beacon chain state transition function (STF) to operate independently of payload availability.

The main motivation is to optimize checkpoint sync operations. By including the `execution_requests_root` in bids, nodes can start syncing from a checkpoint state without needing to request the previous payload, streamlining the synchronization process. Arnetheduck briefly mentions this relates to "top-up sync under ePBS" (enshrined Proposer-Builder Separation).

**Participants:** arnetheduck, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-29T04:47:24.967000+00:00] m.kalinin: why do we put `execution_requests_root` into the `bid`?
[2026-04-29T05:27:23.346000+00:00] potuz: To check that the requests in the block match those committed in the Payload, without having the Payload
[2026-04-29T05:31:32.610000+00:00] m.kalinin: to be able to run beacon chain STF without a payload, i see
[2026-04-29T05:39:25.936000+00:00] potuz: There point is that on checkpoint sync you get the state and we wanted to avoid requesting the previous payload to start syncing
[2026-04-29T06:26:59.192000+00:00] arnetheduck: Top-up sync under ePBS
```

</details>

