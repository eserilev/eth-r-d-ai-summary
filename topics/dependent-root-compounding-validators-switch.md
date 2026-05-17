# Dependent Root Issues and Switch to Compounding Validators

---

## 2026-04-08 (epbs)

This discussion centers on technical issues with dependent roots in Ethereum's beacon chain, particularly problems introduced by the "switch to compounding validators" functionality. Potuz identifies a specific scenario where a validator with a pending deposit increases its balance to 33 ETH at slot 32, then sends a switch to consolidation request at slot 63, followed by a missing block at slot 64. This creates a situation where two post-states differ in effective balances but not in immediately computable committees, leading to different views of proposer and attestation duties when blocks 65-95 are missing. The core issue is that nodes operating on different chain states (with and without payload) will have conflicting views of who should be proposing and attesting.

The participants discuss the broader implications for fork choice and justification mechanisms. There's concern about the fragility of the current construction and its impact on future extensibility of execution requests. A particular challenge emerges around justified balances and checkpoint voting: if a beacon block root receives 2/3 votes but its payload doesn't, it's unclear whether to justify the empty block or not, with both approaches creating downstream problems.

The discussion leaves several open questions unresolved, including the exact mechanics of payload validation timing, the deterministic nature of the dependent_root API, and the broader implications for the justification process when there's a split between beacon block and payload consensus.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

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

