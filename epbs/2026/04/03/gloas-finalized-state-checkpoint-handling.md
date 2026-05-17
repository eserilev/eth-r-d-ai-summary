# Finalized State and Checkpoint Handling in Gloas

**Channel:** epbs | **Date:** 2026-04-03

## Summary

The discussion centers on how to handle finalized state and checkpoint handling in Gloas when blocks are skipped. The main issue arose from an e2e test error where forkchoice selected EMPTY as head when preparing for the next slot, which was resolved by using the next clock slot to trigger Gloas' tie-breaker logic between FULL vs EMPTY variants.

The core debate focused on what constitutes the correct finalized state when a checkpoint slot is skipped (where `n % 32 == 0`). tuyen4818 argued that the finalized state should be the post-envelope state dialed to slot `n` rather than the post-block state of slot `n-1`, emphasizing backward compatibility and the ability to sync from finalized states. This view maintains that finalized states should always have `slot % 32 == 0` and be respective to the checkpoint's epoch. However, 0xunclebill raised concerns about safety guarantees, noting that attestations vote for the target block root `n-1` and questioning whether post-envelope states can be considered truly finalized with the same certainty as pre-Gloas.

The discussion concluded with input from potuz stating definitively that "the finalized state is always post CL block, never post envelope," though acknowledging that finalization could theoretically signal full or empty variants through descendant attestations. An additional implementation question was raised about which block hash to pass for FCU in skip slot scenarios, with Lodestar's approach being to pass `parent_block_hash` for EMPTY variants and `block_hash` for FULL variants.

## Participants

- 0xunclebill
- potuz
- terencechain
- tuyen4818

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
