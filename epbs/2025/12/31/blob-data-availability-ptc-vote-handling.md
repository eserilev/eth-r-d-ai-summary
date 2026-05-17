# PTC Vote Expiration and Fork Choice Logic

**Channel:** epbs | **Date:** 2025-12-31

## Summary

The discussion centers on three main improvements to PTC (Payload Timeliness Committee) vote handling in Ethereum's fork choice logic. First, fradamt suggests making `notify_ptc_messages` explicit in `on_block` and potentially adding `notify_attestations` for consistency. Second, they propose explicitly updating `store.ptc_vote` only when the payload attestation is in the block or when it's the first received PTC attestation, making the current implicit wire-level behavior explicit in fork choice.

The most substantive debate focuses on whether PTC votes should expire after a certain number of slots (e.g., 100). Fradamt argues this would simplify the system by allowing nodes to ignore old messages, avoid synchronization concerns for very old votes, and prevent dangerous disagreements where proposers don't extend payloads but attesters expect them to based on locally stored votes. They propose modifying `is_payload_timely` to return `False` for votes older than `SLOTS_FOR_PTC_VOTE_EXPIRATION`.

Potuz acknowledges that vote expiration would force agreement and isn't strongly opposed, but prefers the simpler approach of requiring proposers to keep PTC votes for fork choice tips during non-finalizing periods. He notes that honest nodes will maintain consistent views, and committee information remains available through parent states. His main concern is potential liveness issues from bugs if defaulting to "not available." Both agree the issue isn't critical either way, with no firm consensus reached on implementing vote expiration.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-31T14:59:56.678000+00:00] fradamt: Seems better to have `notify_ptc_messages` in `on_block` than to leave it implicit, but then shouldn't we also have `notify_attestations`?
[2025-12-31T15:16:58.309000+00:00] fradamt: Also was wondering if it wouldn't be better to explicitly only update `store.ptc_vote` when either:
- the payload attestation is in the block
- the ptc attestation is the first received one, i.e. `ptc_vote[ptc_index]` is not set

This is implicitly already what happens because on the wire we'll only accept the first seen payload attestation for each sender, it's maybe worth making it explicit in the fork-choice logic?
[2025-12-31T15:20:59.593000+00:00] fradamt: Also, should we have an explicit concept of expiration of ptc votes? Right now in principle it could be that there's a payload "hanging" for a while as a leaf of the fork-choice tree, because no beacon block is built on it, or there could be a reorg that brings you to the same situation.

We could perhaps resolve the fork-choice decision between empty or full without relying on ptc votes at that point, e.g. default to empty whenever the payload is older than X slots? So that we can forget about any ptc votes older than X slots as well
[2025-12-31T15:22:48.780000+00:00] potuz: saving PTC votes is not a problem as long as we are finalizing. If we aren't finalizing they will start to pile up, but the states are much worse anyway in this scenario
[2025-12-31T15:24:03.222000+00:00] potuz: notice that the proposer can extend on his head view without including PTC votes if they lost them and count that attesters have the same head view. attesters don't need the vote, they only need to have kept track of the ptc vote in their local forkchoice
[2025-12-31T15:24:23.314000+00:00] potuz: so this only makes a difference in the event that proposer and attesters have different views and the votes were lost
[2025-12-31T15:25:36.944000+00:00] fradamt: What benefit do we get from ptc votes from (e.g.) 100 slots ago though? We could just have `is_payload_timely` default to `False`, and then it would be up to the proposer whether to extend it or not, which seems ok if for some reason there hasn't been any beacon block built on it in (e.g.) 100 slots
[2025-12-31T15:26:59.512000+00:00] potuz: the only benefit we get from the votes is allowing the proposer to assert its view when it has them. So it seems reasonable that the proposer looses this guarantee after a long time without finalization if he starts dropping old votes.
[2025-12-31T15:28:25.381000+00:00] fradamt: If the votes are expired for everyone, the proposer wouldn't lose any guarantee, because attesters' logic becomes:
- if the proposer doesn't extend it, fine (`is_payload_timely` says `False`, as if there were no votes)
- if the proposer does extend it (and it is locally available), fine
[2025-12-31T15:29:03.531000+00:00] fradamt: It removes the dangerous case, which is: proposer does not extend it, but attesters would have liked it to be extended because `is_payload_timely` returns `True` for them (they have a majority of ptc votes locally)
[2025-12-31T15:34:18.727000+00:00] potuz: yeah I agree that expiring votes in forkchoice would force agreement in this scenario, but I believe it adds extra unnecessary complexity: honest nodes will keep track of their view. If for them `is_payload_timely` returns true, they expect it, if the proposer's view is the same it will build on top of present regardless of having kept the votes or not. And if it's different it will be reorged. This doesn't seem to me as a bad situation: the proposer must have known in this situation that a long branch existed which for some reason is not its head and he's building on top of some old tip of forkchoice. At any rate it seems simpler to me to force proposers to always keep the PTC votes of tips of the forkchoice tree when not finalizing
[2025-12-31T15:40:38.505000+00:00] fradamt: It's not a huge deal either way, I think it's nice to be able to completely ignore old messages though, it makes the system easier to reason about, e.g. we don't need to worry about them when syncing, we don't need to worry about keeping the network on the same page on these messages (e.g. if we're in this situation and someone sends out a new ptc vote , we should in principle make sure that either no one gets it or everyone does), we don't have to worry about computing the committee if we don't have it already because it's very old or on a new branch
[2025-12-31T15:42:45.074000+00:00] fradamt: ```python
def is_payload_timely(store: Store, root: Root) -> bool:
    """
    Return whether the execution payload for the beacon block with root ``root``
    was voted as present by the PTC, and was locally determined to be available.
    """
    # The beacon block root must be known
    assert root in store.blocks

    # If the payload is not locally available, the payload
    # is not considered available regardless of the PTC vote
    if root not in store.execution_payload_states:
        return False

    payload_state = store.execution_payload_states[root]
    if get_current_slot(store) < payload_state.slot + SLOTS_FOR_PTC_VOTE_EXPIRATION:
        return sum(store.ptc_vote[root]) > PAYLOAD_TIMELY_THRESHOLD
    
    return False
```
This is what I mean
[2025-12-31T15:48:32.642000+00:00] potuz: yeah I don't think it's a big issue indeed either way. Notice that the committee has to be available anyway though cause we must retrieve the parent state to sync the CL block and that has the right committee. My only worry about defaulting to not available is liveness in case of bugs.  Same reason why I wouldn't like attesters to enforce that the payload be reorged if they didn't hit a threshold of  explicit "not available" PTC votes.
```

</details>
