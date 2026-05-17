# Post Block State Impact on Downstream Consumers

---

## 2026-04-06 (epbs)

This discussion focuses on the technical implications of returning post-block state without advancing in the context of Ethereum's ePBS (enshrined Proposer-Builder Separation). nflaig identifies several breaking changes this approach would introduce: the weak subjectivity specification would need updates (specifically `is_within_weak_subjectivity_period`), finalized states would no longer contain settled validator balances due to skipped epoch transitions, and all downstream consumers of `/eth/v2/debug/beacon/states/finalized` would require fork-aware handling since the `slot % 32 == 0` assumption would be broken.

wemeetagain acknowledges that ePBS fundamentally changes checkpoint semantics, noting that pre-Gloas checkpoints functioned as reference types with a clear checkpoint-to-state mapping, while current ePBS makes this relationship ambiguous and forces all downstream consumers to handle disambiguation independently. Both participants agree this represents a significant departure from existing checkpoint state concepts, with wemeetagain suggesting the discussion of retaining existing semantics is worthwhile, though no final decision or consensus is reached in these messages.

**Participants:** nflaig, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-04-06T21:37:44.454000+00:00] nflaig: few more considerations when we go with returning the post block state (without advancing)
- weak subjectivity spec needs to be updated as well, specifically `is_within_weak_subjectivity_period`
- the `finalized` state no longer has "settled" validator balances since we don't apply epoch transition
- every donwstream consumer of `/eth/v2/debug/beacon/states/finalized` will need fork aware handling since `slot % 32 == 0` assumption is broken
in my opinion this completely breaks the semantics of the checkpoint state concept
[2026-04-06T22:17:44.055000+00:00] wemeetagain: Well the semantics of "checkpoint" is quite different now. Pre-gloas, checkpoint is a reference type. checkpoint -> state is a function. Current ePBS makes that ambiguous. All downstream consumers must disambiguate independently. Maybe thats fine. But retaining the existing semantics is worthy of being discussed and I don't think thats intellectual masturbation.
```

</details>

