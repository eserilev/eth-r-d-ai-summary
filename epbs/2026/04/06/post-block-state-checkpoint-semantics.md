# Post Block State and Checkpoint Semantics Implications

**Channel:** epbs | **Date:** 2026-04-06

## Summary

This discussion focuses on the technical implications of returning post-block state without advancing in the context of ePBS (enshrined Proposer-Builder Separation). nflaig raises several concerns about this approach, including the need to update the weak subjectivity specification (specifically `is_within_weak_subjectivity_period`), the issue that finalized states would no longer have settled validator balances due to skipped epoch transitions, and the breaking of downstream consumers' assumptions about `/eth/v2/debug/beacon/states/finalized` since the `slot % 32 == 0` assumption would no longer hold. They argue this fundamentally breaks the semantics of checkpoint state concepts.

wemeetagain acknowledges that checkpoint semantics have changed significantly with ePBS, noting that pre-Gasper checkpoints were clear reference types with deterministic checkpoint-to-state functions, but current ePBS makes this relationship ambiguous, requiring all downstream consumers to handle disambiguation independently. While they suggest this ambiguity might be acceptable, they emphasize that preserving existing checkpoint semantics is a legitimate concern worth discussing rather than dismissing.

The conversation leaves open questions about how to properly handle these semantic changes and whether the benefits of the proposed approach outweigh the significant breaking changes to existing checkpoint concepts and downstream integrations.

## Participants

- nflaig
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-06T21:37:44.454000+00:00] nflaig: few more considerations when we go with returning the post block state (without advancing)
- weak subjectivity spec needs to be updated as well, specifically `is_within_weak_subjectivity_period`
- the `finalized` state no longer has "settled" validator balances since we don't apply epoch transition
- every donwstream consumer of `/eth/v2/debug/beacon/states/finalized` will need fork aware handling since `slot % 32 == 0` assumption is broken
in my opinion this completely breaks the semantics of the checkpoint state concept
[2026-04-06T22:17:44.055000+00:00] wemeetagain: Well the semantics of "checkpoint" is quite different now. Pre-gloas, checkpoint is a reference type. checkpoint -> state is a function. Current ePBS makes that ambiguous. All downstream consumers must disambiguate independently. Maybe thats fine. But retaining the existing semantics is worthy of being discussed and I don't think thats intellectual masturbation.
```

</details>
