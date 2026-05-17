# Payment Weight Computations and State Management

**Channel:** epbs | **Date:** 2025-12-18

## Summary

The discussion centers around whether payment weight computations could be moved from state storage to epoch boundary processing to avoid maintaining weight data in the state. m.kalinin proposed computing `payment.weight` during payments processing at epoch boundaries, but potuz identified significant technical obstacles: the computation requires information about committee members from specific committees who voted for exact blocks, which isn't readily available during state transitions.

The conversation explored using the TIMELY_HEAD flag as a potential solution, but this approach was deemed insufficient because proposers can choose not to include attestations, and the flag doesn't capture the necessary granular information about which validators voted for specific blocks. Additionally, implementing this would require reading committee shuffling data during epoch processing and accessing validators assigned to committees for specific slots, which introduces complexity concerns.

The discussion concluded that keeping payment weights in the state isn't particularly problematic, effectively settling on maintaining the current approach rather than pursuing the optimization of moving computations to epoch boundaries. No alternative solutions or follow-up actions were proposed.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T05:30:31.684000+00:00] m.kalinin: couple of questions:
1) Did we consider `payment.weight` computations during payments processing at the epoch boundary? this will allow to avoid keeping and updating `weight` in the state. There might be some obstacles to do that -- haven't thought about this approach much.
[2025-12-18T10:06:44.482000+00:00] potuz: Doing this computation in the transition is rather impossible: how do you grab the weight, it needs to be from committee members from that exact committee and they need to have voted for that exact block. We don't keep this info.
[2025-12-18T10:09:00.912000+00:00] m.kalinin: So, TIMELY_HEAD flag and information about whether the block in that slot has a different root than the block from the previous slot won't help?
[2025-12-18T10:10:14.681000+00:00] potuz: Timely head doesn't work cause the next proposer can simply not include those attestations
[2025-12-18T10:13:03.780000+00:00] m.kalinin: The flag is set regardless of the distance, but the reward is then computed with the knowledge of the distance. However, to get validators that has voted for that particular block you will have to use shuffling of the committees of the slot of that block, not sure if it is good idea to read shuffling inside of epoch processing
[2025-12-18T10:18:06.428000+00:00] potuz: https://github.com/ethereum/consensus-specs/blob/master/specs/altair/beacon-chain.md#get_attestation_participation_flag_indices

```
    participation_flag_indices.append(TIMELY_TARGET_FLAG_INDEX)
    if is_matching_head and inclusion_delay == MIN_ATTESTATION_INCLUSION_DELAY:
        participation_flag_indices
```
[2025-12-18T10:18:37.783000+00:00] potuz: Sorry, doing this on a cell is hard 🙂
[2025-12-18T11:02:20.326000+00:00] m.kalinin: i see now, sorry, got confused for the moment, then there is no way to do that with that flag. introducing another flag doesn't sound about right as this change alone won't help, in addition we will have to read validators that were assigned to the committees of that specific slot
[2025-12-18T11:33:44.042000+00:00] potuz: Keeping this in the state is not that problematic also
```

</details>
