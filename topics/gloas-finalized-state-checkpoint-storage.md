# Finalized state handling and checkpoint storage in Gloas

---

## 2026-04-04 (epbs)

tuyen4818 raised concerns about finalized state handling in Gloas, particularly when dealing with skipped slot finalized states. They noted that Lodestar may have similar issues when restarting nodes with skipped slot finalized states if using postState as the finalized state, and emphasized that the spec lacks clarity on this case, requiring deeper investigation for future reference.

The discussion focused on three key technical questions: whether finalized/justified states are equivalent to `store.checkpoint_states[cp]` in the forkchoice spec (assumed true for pre-Gloas but needing clarification for Gloas), whether the Phase 0 `store_target_checkpoint_state` computation is correct for Gloas, and how to properly handle checkpoint state storage. tuyen4818 identified that the current implementation only uses `store.block_states` as the base state, but argued that for Gloas attestations, the base state could also come from `store.payload_states` when dialing up to the target epoch.

A significant concern was raised about potential conflicts when updating `store.checkpoint_states[target]` based on either `store.block_states` or `store.payload_states`, as one could override the other. tuyen4818 suggested that checkpoint states may need to be stored by both checkpoint and payload_status to avoid this issue. No responses or resolutions were provided in the captured messages, leaving these technical questions open for further discussion.

**Participants:** tuyen4818

<details>
<summary>Raw messages</summary>

```
[2026-04-04T08:31:40.187000+00:00] tuyen4818: yes I believe lodestar will also have the same issue when restarting the node with skipped slot finalized state, if we use postState as the finalized state
[2026-04-04T08:47:54.936000+00:00] tuyen4818: my concern is we cannot count on the spec for this case (the finalized state of skipped slot finalized checkpoint), it means that we need to dig into the spec a bit to make it clear for future reference

could you guys give your inputs:
- 1) is finalized/justified state the same to `store.checkpoint_states[cp]` in forkchoice spec? I assume it is for pre-gloas, need to clarify for gloas if it's not
- 2) is the `store.checkpoint_states` computation in phase0 correct for gloas?
```python
def store_target_checkpoint_state(store: Store, target: Checkpoint) -> None:
    # Store target checkpoint state if not yet seen
    if target not in store.checkpoint_states:
        base_state = copy(store.block_states[target.root])
        if base_state.slot < compute_start_slot_at_epoch(target.epoch):
            process_slots(base_state, compute_start_slot_at_epoch(target.epoch))
        store.checkpoint_states[target] = base_state
```

for me the `base_state` is always `store.block_states` which is not correct. Given a specific  gloas attestation (check `on_attestation()` phase0), `base_state` could be from `store.payload_states` too dialing up to target epoch, we need to update that?

- 3) but updating `store.checkpoint_states[target]` based on either `store.block_states` vs `store.payload_states` given an attestation data is also not correct because one would override the other. We need to store `checkpoint_state` by checkpoint + payload_status too?
```

</details>

---

## 2026-04-06 (epbs)

The discussion centers on a disagreement about checkpoint state semantics in the Gloas implementation, specifically whether checkpoint states should be advanced to epoch boundaries. nflaig reports that a recent change breaks checkpoint sync with Lodestar on mainnet, as Lodestar expects states to be advanced to epoch boundaries according to the existing `store_target_checkpoint_state` specification. They reference a previous GitHub discussion on this topic in the beacon-APIs repository.

potuz, who merged the change, argues that advancing the state is "strictly incorrect" in Gloas and that if clients want advanced states, the API should return the post-payload state instead. They acknowledge this would require subtle changes in Prysm and note that returning advanced states could technically allow honest nodes to reorg the checkpoint data. potuz also confirms that most API specifications were intentionally left out of scope for their current Gloas implementation.

The conversation reveals that there are still gaps in the specification regarding checkpoint state semantics for Gloas, with nflaig suggesting that the definition of "checkpoint state" itself needs to be redefined from the phase0 specification. The discussion ends with 0xunclebill suggesting the block state approach is correct, though potuz maintains their position about advancement requirements. No clear resolution is reached, with potuz indicating willingness to discuss further in an upcoming ACDT meeting.

**Participants:** 0xunclebill, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-06T11:22:49.361000+00:00] nflaig: is this intended for the devnet only? I'm pretty sure this breaks checkpoint sync with lodestar on mainnet as we expect the state to be advanced to the epoch boundary, also per `store_target_checkpoint_state` that seems right.
[2026-04-06T11:22:54.059000+00:00] nflaig: I also found previous discussion regarding this here https://github.com/ethereum/beacon-APIs/issues/423
[2026-04-06T11:25:56.094000+00:00] potuz: I merged it in develop, happy to discuss this today in ACDT but in Gloas advancing it is strictly incorrect. If clients want to advance it then we need to return the post Payload state. I don't mind this (although this will be a rather subtle change in Prysm) but technically this will make the checkpoint server return something that can technically be reorged by honest nodes
[2026-04-06T11:26:43.912000+00:00] nflaig: in any case, aren't there gaps in the spec still regarding this for gloas?
[2026-04-06T11:27:17.751000+00:00] potuz: Yes, I left most of the API intentionally out of the scope I targeted for
[2026-04-06T11:27:35.777000+00:00] nflaig: no but I mean `store_target_checkpoint_state` is phase0
[2026-04-06T11:28:10.337000+00:00] nflaig: we need to redefine what a checkpoint state means
[2026-04-06T11:31:12.551000+00:00] 0xunclebill: it’s the block state
[2026-04-06T11:31:24.750000+00:00] 0xunclebill: that looks correct to me?
[2026-04-06T11:31:36.636000+00:00] potuz: not if it's required to be advanced
```

</details>

---

## 2026-04-09 (epbs)

This discussion centers on a fundamental issue with FFG (Finality Gadget) consensus semantics in the current Gloas specification versus a proposed change by Nico. The core problem identified by wemeetagain is that the current Gloas spec creates ambiguity in checkpoint states because payload processing occurs before epoch transitions, leading to situations where justified balances might be calculated from non-canonical chain states. This results in two possible states at slot boundaries that are conflated by the consensus protocol, causing information loss where attesters voting for the "same thing" are actually on different forks (payload applied vs. not applied).

The proposed solution moves execution request processing to occur after epoch processing rather than before, which would restore the checkpoint-to-state function and eliminate the ambiguity. Potuz argues that while this change would improve semantics and simplify implementation by avoiding dual state transitions, it doesn't actually constitute a consensus-breaking change since the underlying FFG guarantees remain the same - attestations are still for post-CL blocks and justified checkpoints use the same state for balance calculations. However, wemeetagain emphasizes that the semantic clarity is crucial for preserving the integrity of checkpoint semantics and reducing implementation complexity.

The discussion reveals that Nico's approach would maintain dependent roots functionality unchanged (seen as the biggest benefit) while eliminating the ability to prove against intermediate post-EL block states, though this capability was likely not useful anyway. Both parties agree the change wouldn't affect checkpoint sync mechanics significantly, as previous epoch payloads would still need to be requested regardless of when epoch transitions occur.

**Participants:** potuz, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-04-09T14:31:54.096000+00:00] wemeetagain: The argument for why its wrong is that the state without the payload applied dialed to that epoch would typically not be part of the canonical chain but part of a competing chain. The balance calculations the protocol chooses should always be from the canonical chain.
- justified balances are pulled from the post-state of a justified root, importantly, dialed to the justified epoch if necessary (skip slots).
- current gloas spec requires the payload to be applied/processed before the epoch transition.
- payload processing affects balances
- thus payload status is a required element in determining the correct balances
[2026-04-09T14:32:35.904000+00:00] wemeetagain: The situation described is the same scenario as, after the end of an epoch, 2/3 - 1 target votes for block at slot 30, and 1 target vote for a descendant at slot 31. We don't worry that we should have justified root from 30 because that chain transitively got enough weight. FFG votes tally towards consensus of the view of the chain at an epoch boundary (after the epoch transition). So it doesn't matter if a supermajority agreed on the chain state at slot 30. By the time the epoch rolled around, they had diverged! The threshold for consensus FFG cares about was not met.

So really what's framed as a problem is actually a good thing that is masked right now. Current gloas spec weakens the consensus protocol's FFG guarantee by failing to definitively come to consensus on an epoch with prior skip slot -- without extra-consensus-protocol information, fork choice info. This is the regression from mainnet that should be fixed one way or another.
[2026-04-09T14:35:08.318000+00:00] wemeetagain: I think <@586161934425128960> 's proposal ^ does the trick without changing checkpoint tho
[2026-04-09T14:35:48.195000+00:00] wemeetagain: just apply exe requests to the state after epoch processing, and the coherence of checkpoint states is restored
[2026-04-09T14:41:05.265000+00:00] potuz: this is not true, attestations today are for block_roots, not pairs block+hash.  To justify 30 as target and not 31 then there must have been a fork and a chain voting descending from 30 and not containing 31. However, with the current vote counting on Gloas, it could happen that we justify 31 without it's payload and still the payload be canonical. 

Of course we *could* deal with this situation as well and make it analogous to the situation you describe of 30/31, in which case we should count also target votes as different justification targets and consider the fork of full/empty at 31. That is, any vote for say, a block in 37 that descends from full and that votes for target 31, count for justification of (31,full) and **not** count for justification of (31,empty), and a vote for a block that descends from (31, empty)  using the root of 31 for target to count for (31,empty) and **not** count for 31, full, but this then leads to even deeper consensus changes.

All over issues that are not consensus breaking.
[2026-04-09T14:43:47.042000+00:00] potuz: by the way, Nico's proposal does not change at all the situation on FFG guarantees: today the guarantee is on the post CL block of the justified root, and that would be exactly the case with Nico's change, as that would be the only post-state
[2026-04-09T14:51:26.674000+00:00] wemeetagain: Yes, with the current gloas spec, this situation is obscured. Payload applied before epoch boundary vs not applied is a fork. But attesters on both sides of the fork are voting for 'the same thing'. Fixing this issue is a big gloas spec change, but its just preserving the guarantees of phase0-fulu.
[2026-04-09T14:52:44.483000+00:00] potuz: it does preserve the same guarantees, that is the whole point, the target vote is always for tha post-CL block, the justified checkpoint is that state, it was before Gloas, it continues to be after gloas, and it would be even if we adopt Nico's change.
[2026-04-09T14:53:33.829000+00:00] wemeetagain: His proposal does change the FFG guarantees.
> today the guarantee is on the post CL block of the justified root
This is not the full picture, tho. It's the post CL block of the justified root, critically at the justified epoch.
[2026-04-09T14:53:47.639000+00:00] potuz: So I continue to streess: there is currently no consensus-breaking issue in the spec, there are tons of bug prone complexities due to having separate state transitions, there are API calls that are broken, semantics of dependent roots that are broken, etc. But there is no consensus breaking change
[2026-04-09T14:55:02.907000+00:00] potuz: that is not the case, the spec only defines justified checkpoint, not states, the API for checkpoint sync is what imposed this condition of advaning the state.
[2026-04-09T14:55:24.399000+00:00] potuz: And that API was discussed in the issue linked above. Before that discussion, no notion of "justified state" existed.
[2026-04-09T14:56:14.932000+00:00] wemeetagain: Thats not correct tho. Its in the fork choice spec. This is mechanically how justified balances are calculated -- from the justified checkpoint state
[2026-04-09T14:57:04.085000+00:00] potuz: That is not broken as far as I can tell
[2026-04-09T14:58:46.281000+00:00] potuz: And again, if we move to Nico's design, the outcome would be that the balances used would be exactly the balances that we would use today in the situation of a missing block in slot 32
[2026-04-09T14:59:12.298000+00:00] potuz: so absolutely nothing would change except just calling a state boundary aligned
[2026-04-09T14:59:53.289000+00:00] wemeetagain: yes but the difference is that there's no ambiguity in the FFG vote any more. Theres no more information loss. Imo restores a lot of sanity
[2026-04-09T15:01:53.637000+00:00] potuz: there is no ambiguity today either I claim, we really would just move literally to the exact same situation as today, except that the "state" would be forced to be an epoch boundary state. I do recognize some big advantage on not having to deal with two official state transition, with some disadvanteges of having to change the signature of the STF, but there are no consensus changes as far as I can tell
[2026-04-09T15:04:09.755000+00:00] potuz: notice that with Nico's change the exact situation can still happen after justification/finalization, the last payload can be reorged, there is no ambiguity and no extra FFG guarantees at all
[2026-04-09T15:04:29.133000+00:00] potuz: and most importantly there is the exact same loss of information
[2026-04-09T15:04:41.381000+00:00] potuz: we aren't counting the votes for full that the desending chain has cast
[2026-04-09T15:06:15.428000+00:00] potuz: The **only** way of accounting for the full votes and preventing the last full payload from being reorged is to change entirely the full justification/finalization in-state approach
[2026-04-09T15:06:57.576000+00:00] wemeetagain: its more a matter of what the chain claims consensus over. In Nico's proposal, even tho fork choice determines payload existance / validity in the current slot in the second phase, the chain/consensus defers that fully to the next slot
[2026-04-09T15:07:44.078000+00:00] potuz: today it is exactly the same thing, attestations are for the post-CL block
[2026-04-09T15:07:54.960000+00:00] potuz: and the payload validity is deferred until the next slot
[2026-04-09T15:08:45.620000+00:00] wemeetagain: Its not the same thing because there are two different possible states at the end of a slot, before the next block is built. And our on-chain consensus loses information and blurs both outcomes (which are divergent) into one.
[2026-04-09T15:10:14.849000+00:00] potuz: we are going in circles, there is the exact same loss of information in both approaches: the CL chain has achieved consensus that the target was slot 31, the votes were enough to justify 31 full, but we do not keep track of this, the block in 33 can build on top of both 31 full or 31 empty and all of the voting for 31 full are neglected
[2026-04-09T15:10:21.405000+00:00] potuz: in either scenario
[2026-04-09T15:10:44.122000+00:00] potuz: we still have the exact same information on chain
[2026-04-09T15:12:43.078000+00:00] wemeetagain: This is why its not a loss of information. In nico's approach, FFG checkpoints lead to a single state. There is no claim about processing that happens after a checkpoint epoch start slot. In the current gloas spec, FFG checkpoints have two outcomes, determined by off-chain fork choice. But the chain has made a claim about what has happened up to that checkpoint epoch start slot. This is the loss of information.
[2026-04-09T15:13:44.642000+00:00] potuz: this is not the case, the state that is used for balance counting is exactly the same! in both today Gloas or Nico's approach, there are no two different outcomes of FFG checkpoints today, it is the post-CL block advanced.
[2026-04-09T15:13:50.565000+00:00] potuz: and that will be in Nico's approach
[2026-04-09T15:14:16.498000+00:00] potuz: The beauty of Nico's approach is that that state advanced will be the state to which we would apply the payload if if existed, which is not the case on today's Gloas
[2026-04-09T15:14:28.650000+00:00] potuz: that is the main difference of having it aligned or not
[2026-04-09T15:14:34.488000+00:00] potuz: but it's not a consensus change
[2026-04-09T15:15:17.687000+00:00] potuz: And the whole point that I am making is that that state in either case has no information about the existence of the payload associated to that root
[2026-04-09T15:18:57.057000+00:00] potuz: moreover notice that it doesn't really help you on checkpoint sync either: since you can't sync the next block unless you request the previous epoch payload anyway, on both approaches you **will** need to request previous epoch's payloads as well, the mechanics of checkpoint sync wont change at all!
[2026-04-09T15:19:20.381000+00:00] potuz: only difference is when you make the epoch transition
[2026-04-09T15:20:12.515000+00:00] wemeetagain: > In [current] Gloas ... it is the post-CL block advanced
The problem conceptually with that is that is that you end up using a non-canonical state to get justified balances! It's consistent, but wrong.
[2026-04-09T15:20:43.960000+00:00] potuz: I understand this complain, which again is a semantics issue, not a consensus problem
[2026-04-09T15:20:58.839000+00:00] potuz: and it is just giving a different name to the exact same state
[2026-04-09T15:21:05.481000+00:00] potuz: that will have to be kept in memory anyway
[2026-04-09T15:22:18.704000+00:00] potuz: So as far as I can see you don't fix the problem of checkpoint sync, you do not change what state is kept in forkchoice,  but there are some gains in not having two state transitions, and dependent roots work unchanged
[2026-04-09T15:23:22.273000+00:00] potuz: Dependent roots working unchanged is the biggest win IMO
[2026-04-09T15:30:56.485000+00:00] potuz: So perhaps the only thing that would be lost with this approach is the ability to prove post-EL blocks states
[2026-04-09T15:30:57.723000+00:00] wemeetagain: Its a really important semantic point to nail down and preserve. (Keep checkpoint -> state function, or alternatively, don't let checkpoints lose information or require additional information) Will break a lot less of our thinking and our code. And anyone using the chain's code.
[2026-04-09T15:31:57.249000+00:00] wemeetagain: Yeah exactly, EL on-chain processing moves entirely to the next block
[2026-04-09T15:32:21.824000+00:00] potuz: not only that, you don't have an intermediate state-root to prove against, but that was probably useless anyway
[2026-04-09T15:33:48.571000+00:00] wemeetagain: and you gain a usable intermediate state state after epoch processing  (this is the "checkpoint state")
```

</details>

