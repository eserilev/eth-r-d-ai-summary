# Payload bid verification and builder tracking

---

## 2026-03-25 (epbs)

The discussion centers on whether signature verification is necessary for payload bids that will be ignored due to lower values, specifically for the purpose of tracking which builders have been "seen" for a given slot. 0xunclebill initially questioned if signature verification was still required for lower-value bids to mark builders as seen for the `(builder, slot)` pair, even when a higher bid already exists for the same `(slot, parent_hash, parent_root)`.

Potuz argued against this approach, emphasizing that DOS prevention should be the primary concern and that it's more efficient to simply drop lower-value bids without verification or tracking. He explained that if the same builder later sends a higher bid, the signature can be verified at that point, and since non-highest bids won't propagate anyway, there's no need for the additional verification overhead.

The consensus reached was to prioritize efficiency and DOS prevention by dropping lower bids immediately without signature verification. Potuz suggested that if stricter tracking is desired, an unverified queue could be implemented to check signatures only on repeat occurrences, but the simpler approach of immediate dropping appears to be the preferred solution.

**Participants:** 0xunclebill, potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-25T05:04:24.764000+00:00] 0xunclebill: for payload bid verification, in some cases where we'd want to ignore a bid, we still have to do signature verification so that we can mark the builder as "seen" for that slot, right?

e.g. we've already seen a bid with a higher value for that `(slot, parent_hash, parent_root)` but this is the first time we've seen a bid for this `(builder, slot)` so we need to perform signature verification and mark the `(builder, slot)` as "seen".
[2026-03-25T13:38:08.853000+00:00] potuz: Why do you want to mark the builder as seen? if you're ignoring and not rebroadcasting a bid, there's no need to even check the signature nor mark the builder as seen, you can drop it before the seen check, if that same builder sends another higher one you can verify the signature and keep it.
[2026-03-25T15:44:30.466000+00:00] 0xunclebill: ah okay I wasn’t sure if we were trying to prevent the same builder from broadcasting different bids in the same slot
[2026-03-25T15:47:07.434000+00:00] potuz: I think we first and foremost want to prevent a dos. It's cheaper to just ignore and that's it. If we want to be really picky about this you can add an unverified queue of seen and only check signatures on the second occasion. If the bid is not the highest one anyway it won't propagate so I think it's fine to just drop without checking
```

</details>

