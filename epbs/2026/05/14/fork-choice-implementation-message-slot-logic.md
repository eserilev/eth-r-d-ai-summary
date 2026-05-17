# Fork Choice Implementation and Spec Changes Discussion

**Channel:** epbs | **Date:** 2026-05-14

## Summary

The discussion centers on fork choice (FC) specification changes and their impact on implementations. m.kalinin clarifies that the proposed changes are purely spec-level modifications that shouldn't affect most implementations since fork choice implementations typically differ significantly from the specification. The main potential impact would be for implementations that use the `is_supporting_vote` function as specified, which would need to be replaced with a new function as outlined in the referenced PR.

potuz explains that their implementation predates the specification and doesn't use the spec's functions directly, having only minimally adapted to the new proposer boost (PB) based reorg system after Francesco's modifications. They express uncertainty about which previous backports may have caused issues with long justified checkpoint reorgs under withholding scenarios, indicating they will investigate this further while merging equivocation checks.

m.kalinin confirms there are no backports in the current PR yet, planning to work on them separately after the current changes are merged. Given that potuz's implementation doesn't rely heavily on the spec's abstractions, m.kalinin concludes the current PR should not pose any problems for their implementation.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-14T04:43:51.618000+00:00] m.kalinin: sure! These changes shouldn't affect implementations as FC impls are quite different comparing to the spec. Why would you want to change anything in your code if that's purely a spec change? It maybe that your code have `is_supporting_vote` as it is in the spec then you might want to replace it with new function as the PR does. But, in general why any FC *spec* backporting would affect impls?
[2026-05-14T11:58:27.096000+00:00] potuz: I don't have any of the functions as in the spec since my implementation predates the spec :), Francesco changed a bunch of the logic I had in the original spec and I minimally adapted to the new PB-based reorg system. I am uncertain now as to which backports there caused trouble with some of our long justified checkpoints reorgs under withholding. I will tackle them soon now that I am merging the equivocation checks.
[2026-05-14T17:09:26.834000+00:00] m.kalinin: I see. There are no backports in the PR yet, I am planning to work on it separately after current PR is merged. As you don't use abstraction of the the spec much in the impl then this PR should be completely fine for you
```

</details>
