# Payload bid verification and builder marking

**Channel:** epbs | **Date:** 2026-03-25

## Summary

0xunclebill asked whether payload bid verification should still perform signature verification for bids that would be ignored (due to lower value) in order to mark builders as "seen" for a given slot, specifically when it's the first bid from that builder for that slot even if higher-value bids already exist for the same `(slot, parent_hash, parent_root)`.

Potuz clarified that there's no need to mark builders as "seen" for ignored bids, as this creates unnecessary computational overhead. If a bid is being ignored and not rebroadcast, it can be dropped before signature verification and seen checks. If the same builder later sends a higher bid, the signature can be verified at that point. The primary goal is preventing DoS attacks, and simply ignoring low-value bids is the most efficient approach.

The discussion concluded that bids can be dropped without signature verification if they won't be the highest bid anyway, since they won't propagate through the network. Potuz suggested that if stricter tracking is needed, an unverified queue could be used to track seen bids and only verify signatures on repeated submissions.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-25T05:04:24.764000+00:00] 0xunclebill: for payload bid verification, in some cases where we'd want to ignore a bid, we still have to do signature verification so that we can mark the builder as "seen" for that slot, right?

e.g. we've already seen a bid with a higher value for that `(slot, parent_hash, parent_root)` but this is the first time we've seen a bid for this `(builder, slot)` so we need to perform signature verification and mark the `(builder, slot)` as "seen".
[2026-03-25T13:38:08.853000+00:00] potuz: Why do you want to mark the builder as seen? if you're ignoring and not rebroadcasting a bid, there's no need to even check the signature nor mark the builder as seen, you can drop it before the seen check, if that same builder sends another higher one you can verify the signature and keep it.
[2026-03-25T15:44:30.466000+00:00] 0xunclebill: ah okay I wasn’t sure if we were trying to prevent the same builder from broadcasting different bids in the same slot
[2026-03-25T15:47:07.434000+00:00] potuz: I think we first and foremost want to prevent a dos. It's cheaper to just ignore and that's it. If we want to be really picky about this you can add an unverified queue of seen and only check signatures on the second occasion. If the bid is not the highest one anyway it won't propagate so I think it's fine to just drop without checking
```

</details>
