# Comparing PR options 4992 vs 5020 implementation tradeoffs

---

## 2026-03-20 (epbs)

The discussion centers on choosing between two implementation approaches for Ethereum client development: PR #4992 and PR #5020. **PR #5020** is more efficient as it only triggers state rehashing when changes are needed, but is considered "hacky" and less general. **PR #4992** is cleaner architecturally but requires more frequent state rehashing, including for empty slots, which impacts performance.

The key technical tradeoffs involve different approaches to handling the PTC (Proposer Target Committee) cache and state management. PR #4992 adds hashing every slot and lacks flexibility for validation during reorgs, while PR #5020 creates UX complexity by requiring different data access methods depending on slot numbers. However, potuz advocates for having full lookahead/behind capability in state, noting that similar caching issues with proposer ID were resolved by implementing lookahead directly in state rather than relying on separate caches.

The team has implemented both approaches for evaluation, with slight preference emerging for PR #5020 despite its drawbacks. An open question remains about implementing next epoch lookahead functionality, and the discussion awaits input from an unspecified team member before reaching a final decision.

**Participants:** nflaig, potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-03-20T08:33:52.463000+00:00] tbenr: I can't decide between 5020  and 4992. The first is more efficient (trigger rehashing only when the change is needed) but a bit "hacky" and less general. The latter is cleaner but requires more state rehashing also when technically not needed (and slows down the processing of empty slots)

I probably need to let the ideas settle
[2026-03-20T10:37:24.511000+00:00] nflaig: I was fine with #4992 before looking into the implement in more detail, at least for us it's cheaper to access the data outside of the state anyways and we cache the ptc for the whole epoch at slot=0 anyways, also something to be careful with, if you load the state you need to make sure the slot is dialed forward to the correct slot.

We have both #4992 adn #5020 implemented, personally have slight preference for #5020 but it might be specific to lodestar implementation wise
[2026-03-20T13:38:24.648000+00:00] potuz: The problem I have with 4992 is that it adds hashing every slot and it's also not flexible in which state to grab for validation, on reorgs it sucks. The problem I have with 5020 is that it just sucks the UX of having to use either state or a computation depending on the slot number. Of course if everything is cached you can use a cache, but that falls into the usual track of actually having  the cache in sync with what's the current state. This is why I believe it's better to have the full lookahead/behind on state. I can say for certain that all the bugs that we had because of a cache miss on the proposer id cache, were completely solved by having the lookahead on state, so I envision the same thing for the PTC.
[2026-03-20T14:09:43.369000+00:00] nflaig: well I was always a fan of that one from the beginning, agree we should solve the next epoch lookahead in that case, also if we do that we don't cache the ptc for just that one edge case, it's actually useful for the lookahead too, curious what <@1035747431461167125> thinks
[2026-03-20T14:10:36.678000+00:00] nflaig: #5020 just exists because I dislike #4992
```

</details>

