# ForkChoiceNode and payload_status simplification discussion

**Channel:** epbs | **Date:** 2026-04-13

## Summary

The discussion centers around whether `ForkChoiceNode` and its `payload_status` field can be simplified following PR #5094, which removes intermediate payload states. Tuyen4818 argues that since `payload_states` is being removed and `payload_status` appears only used in `get_head()`, the fork choice specification could be significantly simplified to align more closely with the "fulu" approach. However, 0xunclebill and tbenr point out that payload status tracking remains necessary for handling PTC votes, attestation weights, and the three payload variants (FULL, EMPTY, PENDING) across lagging validators and missed slots.

The conversation reveals different perspectives on implementation versus specification. Wemeetagain suggests that with single-phase state transition, payload status could be tracked via parent payload hash relationships rather than explicit status fields. Potuz clarifies that `ForkChoiceNode` is an implementation artifact from Prysm and that PR #5094 doesn't actually change fork choice logic - the removal of intermediate payload states is orthogonal to fork choice requirements. The consensus emerges that while the implementation details could vary, fork choice must still distinguish between empty and full branches and handle the three payload variants, regardless of the specific data structure used.

## Participants

- 0xunclebill
- potuz
- tbenr
- tuyen4818
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T03:51:38.312000+00:00] tuyen4818: with the current gloas spec, `ForkChoiceNode` is mandatory because the same block root addresses two physically distinct states `(block_states[root] vs payload_states[root])` — fork-choice has to be able to express which state a head points to.
but with https://github.com/ethereum/consensus-specs/pull/5094 do we still need `ForkChoiceNode` to carry `payload_status`? I don't see any consumers of it in the spec, `payload_states` is removed
if we also remove `payload_status` in `ForkChoiceNode`, the forkchoice spec is a lot simplified and much more closer to fulu
[2026-04-13T05:31:27.289000+00:00] 0xunclebill: Fork choice still needs a way to express payload status. Without `ForkChoiceNode` I'm not sure it would be able to do so. Where would ptc votes and attestation payload weights land?

I'm seeing `is_supporting_vote` `get_node_children` and
 `get_payload_status_tiebreaker`  read it just to name a few
[2026-04-13T06:25:25.447000+00:00] tuyen4818: it's confusing to me
all of that are used for `get_head()`
and `get_head()` is used for head vote, `payload_status` is used for `attData.index == 1` for skipped slot
then it's used inside `get_head()` again
so the only function is `get_head()`

in term of lodestar's implementation, this design maps to 3 different variants with same `blockRoot + stateRoot`. In the current gloas it makes a lot of sense, I'm just not sure after this PR is applied

there could be some use cases other than `get_head()` that I'm missing? if not it opens a way for simplification
[2026-04-13T06:32:56.246000+00:00] 0xunclebill: in `get_proposer_head` via `is_head_weak` and `is_parent_strong` as well. but yeah maybe there is a way to simplify, though I think the concept of payload status still needs to live somewhere in fork choice
[2026-04-13T06:44:23.520000+00:00] tuyen4818: yes `get_proposer_head` uses `is_head_weak` which uses `get_attestation_score`
but it's not needed in this context, it should work the same to fulu, there is no need to separate FULL vs EMPTY vs PENDING in this specific scenario
[2026-04-13T06:45:51.924000+00:00] tuyen4818: I think a way to look at this forkchoice design is start from fulu instead of the current gloas to make sure we don't overcomplicate things and introduce technical debt for future forks
[2026-04-13T09:22:47.646000+00:00] tbenr: I share confusion.
But in my mental model, don't we need anyway to handle votes for the 3 variants? FC must always have to count votes for lagging validators (not seeing block at N and voting for N-1 root with index) voting for FULL and EMPTY and count votes for the non lagging ones voting for PENDING.  And those votes can be seen scattered along a 64 slots moving window.
Am I correct?
[2026-04-13T09:40:28.099000+00:00] 0xunclebill: also not just lagging validators but missed slots as well
[2026-04-13T09:42:50.400000+00:00] tbenr: sure, I was just emphasizing the non perfect-hipothetical case
[2026-04-13T12:36:47.387000+00:00] wemeetagain: I think now that we have single phase stf, we can track pending/full via parent payload hash and payload hash.
"full" means the parent payload hash is N-1 payload hash
"empty" means parent payload hash is N-2 payload hash
[2026-04-13T12:47:36.195000+00:00] potuz: This is the right way of thinking, instead of focusing on the python implementation aspects of it, one should think on the actual spec that Python is trying to express. ForkchoiceNode is an old artifact of how I chose to implement this in Prysm years ago. I'm pretty sure a gazillion different constructions would work just the same and some may be better adapted to each language. The fact of the matter is that forkchoice needs to pick between empty or full branches, choose whatever you want to use to make that distinction. Today forkchoice uses that construction of ForkChoiceNode to avoid duplication
[2026-04-13T12:51:23.030000+00:00] potuz: This PR does not change forkchoice in any way, the dissapeareance of the intermediate payload state is irrelevant to forkchoice.
[2026-04-13T13:02:04.115000+00:00] tbenr: I think stf is orthogonal to FC.
As Potuz just said, you just have to have a data structure supporting the vote counts of this combinations. and "pending" is a legit third variant.
the absence of an intermediate state has not changed the FC by any means to me.
```

</details>
