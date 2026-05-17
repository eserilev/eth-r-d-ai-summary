# Gloas Implementation and Equivocation Handling

**Channel:** epbs | **Date:** 2026-02-24

## Summary

The discussion focuses on implementation challenges for Gloas (a proposed Ethereum protocol upgrade) and equivocation handling. tbenr discovered they had only implemented `update_proposer_boost_root` but not `should_apply_proposer_boost` in their Gloas implementation, raising concerns about potential issues with pre-Gloas functionality that need further investigation.

Both developers are struggling with equivocation-related implementations post-Gloas. potuz has avoided implementing equivocation handling due to complexity around committee computation, while for `is_head_weak` they've opted to cache justified balances in forkchoice and calculate averages across slots. The specific challenge involves the code block that iterates through committee indices and sums effective balances for validators in `store.equivocating_indices`, which potuz has not yet implemented.

tbenr notes that the new `is_head_weak` function is particularly tricky, hoping that all necessary committees are already precomputed and cached in state. Both developers express apprehension about equivocation handling complexity, with potuz planning to defer this implementation and tbenr unable to provide input on the specific equivocation code snippet at this time.

## Participants

- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-24T17:26:30.505000+00:00] tbenr: Oh. that's a good question. I've focused only on the `update_proposer_boost_root` . I have not implemented `should_apply_proposer_boost` in gloas. But now I have the fear i lost something for pre-gloas. I need to take another round.
[2026-02-24T17:28:30.010000+00:00] potuz: I haven't yet implemented anything related to equivocations, post-Gloas, the committee computation scares me
[2026-02-24T17:34:04.221000+00:00] tbenr: oh the new  `is_head_weak` is tricky. we cache all the committees in state. I hope that we have all there already precomputed
[2026-02-24T17:34:32.907000+00:00] tbenr: equivocation already scares me 🙂
[2026-02-24T17:35:50.622000+00:00] potuz: for is_head_weak I just cache the justified balances in forkchoice and take the sum of all and divide by the number of slots. But equivocations require a finer balance handling
[2026-02-24T17:36:10.631000+00:00] potuz: Don't even know how to do that now, so will leave it for down the road
[2026-02-24T17:39:13.465000+00:00] tbenr: are you referring to `if i in store.equivocating_indices` ?
[2026-02-24T17:40:18.954000+00:00] potuz: ```
    for index in range(get_committee_count_per_slot(head_state, epoch)):
        committee = get_beacon_committee(head_state, head_block.slot, CommitteeIndex(index))
        head_weight += Gwei(
            sum(
                justified_state.validators[i].effective_balance
                for i in committee
                if i in store.equivocating_indices
            )
        )
```
Yes, this part I have not implemented yet
[2026-02-24T17:42:11.468000+00:00] tbenr: Can't comment yet 🙂
```

</details>
