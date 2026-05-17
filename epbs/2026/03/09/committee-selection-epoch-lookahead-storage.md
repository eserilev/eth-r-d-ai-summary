# Committee Selection and Epoch Lookahead Storage

**Channel:** epbs | **Date:** 2026-03-09

## Summary

The discussion centers on optimizing storage requirements for committee selection lookahead mechanisms while maintaining safety. fradamt notes that unweighted selection is unsafe with current committee sizes and proposes an "ugly alternative" to achieve one-epoch lookahead without storing three full epochs: shifting the storage window to cover 64 slots starting from the last slot of epoch N-1, storing only what's needed for epoch N operations.

fradamt also suggests a slot-based lookahead approach that would store only [previous slot, previous slot + 31] and update during `process_slot`, which would significantly reduce storage to just a couple of slots. potuz acknowledges this slot-based approach but characterizes it as "nightmarish" to implement, while noting that if clients aren't concerned about the 256KB storage overhead, the epoch-based approach remains simpler to implement and reason about. The discussion concludes without a firm decision, weighing implementation complexity against storage efficiency.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T11:12:24.018000+00:00] fradamt: Do you mean in the devnet, or in general? Not weighting the selection is not safe with this committee size
[2026-03-09T11:13:49.241000+00:00] fradamt: A very ugly alternative to have a one epoch lookahead without storing 3 epochs would be to shift the window back by one 😅
[2026-03-09T11:14:24.182000+00:00] potuz: I didn' t get this one
[2026-03-09T11:17:06.236000+00:00] fradamt: Instead of storing epoch N-1 and N, store 64 slots starting from the last one of epoch N-1 (which is the only one we need in epoch N). Anyway, doesn't really seem worth considering
[2026-03-09T11:20:14.827000+00:00] fradamt: Or could it be a slot-based lookahead instead of epoch-based, always storing [previous slot, previous slot + 31] and being updated in `process_slot`?
[2026-03-09T11:44:16.716000+00:00] potuz: ohhh yeah that probably will be a little nightmarish :). The slot based lookahead I did consider, that way it reduces to just a couple of slots. But it's true that if clients don't really care much about the 256KB the epoch based one is simpler to reason about.
```

</details>
