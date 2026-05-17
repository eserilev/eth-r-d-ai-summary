# GLOAS Dependent Root Issues and Analysis

**Channel:** epbs | **Date:** 2026-04-08

## Summary

Potuz shared an analysis of dependent root issues in GLOAS, highlighting a critical problem where validator balance changes from execution requests can create divergent post-states that affect committee assignments and proposer duties. The main scenario involves a validator receiving a deposit that increases their balance to 33 ETH, followed by a switch to consolidation request, which creates different effective balances depending on whether the payload is included or missing. This divergence becomes problematic because the dependent root mechanism fails when post-states differ in lookahead calculations for proposers and PTC duties, particularly when multiple consecutive blocks are missing.

The discussion reveals that `switch_to_compounding_validator` is especially problematic, and nflaig notes that the dependent root construction is fragile and may limit future extensibility of execution requests. A key technical point emerges around justified balances - even when nodes use the same "wrong" justified balances, they remain aligned, but the broader issue persists about how to handle justification when block roots receive 2/3 votes but payloads don't.

Several open questions remain unresolved, including the deterministic nature of the dependent root API, the downstream implications of payload validation timing (where invalidity is checked with the next beacon block rather than immediately), and the fundamental challenge of maintaining consensus when execution requests create state divergences that affect validator duties across multiple epochs.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-08T13:56:21.079000+00:00] potuz: Forgot to upload this, added a last section with the issue of dependent roots, <@688748669268132001> could you check if this matches your thinking? https://www.potuz.net/posts/gloas-finalized/#analysis-of-dependent-root I didn't really check these, but if what's written there is true then the only real issue I could find is this. 

- Slot 32 a pending deposit is applied and a validator top up increases balance to 33 ETH on a validator. It's not taken by the sweep. 
- Slot 63 that same validator sends a switch to consolidation request. 
- Slot 64, the beacon block is missing. 
- Slot 65: the post states of 63 with the payload and 63 without the payload differ in effective balances (that validator has > 32 on one state and 32 in the other), but do not differ in any of the committees that can be computed yet for this epoch: PTC and Proposers are in the lookahead, active validators are the same. Forkchoice vote counting also does not change yet cause the balances that are being used are still on the justified state in slot 32. 
- At slot 64 however in the epoch transition, the poststates will also differ in the lookahead for proposers and PTC duties. The proposers for epoch 3 (starting in slot 96) and the PTC attesters for that epoch will be different due to the different effective balances. 
- Slots 65--95 are missing: now during slot 96 the two post-states are still different on the effective balance. So dependent root is not useful during this epoch to determine duties: those that are acting on top of empty have a different view of who's proposing/attesting than those that are acting on top of full. 


If that switch to compounding situation did not exist, the same problem woudl manifest itself but only with 4 epochs of missing blocks when exits/deposits start making a difference in duties.
[2026-04-08T14:40:05.758000+00:00] nflaig: I was also looking into this and seems like it's mostly `switch_to_compounding_validator` that is problematic but the whole construction is fragile and will limit the extensibility of execution requests in the future, also for what's the purpose of `dependent_root` current is on the api it isn't deterministic, the `proposer_lookahead` can change without the `dependent_root` changing if I am not wrong
[2026-04-08T14:41:42.160000+00:00] nflaig: is that the case because every node will use the same (wrong) `get_weight` so nodes are all aligned on the wrong justified balances?
[2026-04-08T14:43:37.292000+00:00] nflaig: >  invalidity of the payload itself is not checked with the payload but rather with the next beacon block
what does that part mean, I don't see how the node would see the payload as valid and invalidate it later with the next block, are you referring to execution requests being processed in the next block?
[2026-04-08T15:10:31.636000+00:00] potuz: >  invalidity of the payload itself is
[2026-04-08T15:18:39.111000+00:00] potuz: I was also looking into this and seems
[2026-04-08T15:40:45.341000+00:00] potuz: yes, I don't think this is *wrong* though, these are the justified balances. Even if we change the checkpoint to contain the payload status the situation is still rather brutal: if the root of 31 gets 2/3 of the vote but the payload does not get the 2/3 of the vote, are we justifying empty or not? Either way is a problem. If we justify empty then we still have this problem of advancing the justified checkpoint and now this state being explicitly forked from the canonical chain. If we don't then we lose justification even with 2/3 of the vote.
[2026-04-08T15:41:01.921000+00:00] potuz: The downstream implications of even that situation are not clear to me
```

</details>
