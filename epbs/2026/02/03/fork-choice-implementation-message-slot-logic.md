# Fork choice voting logic bug fix discussion

**Channel:** epbs | **Date:** 2026-02-03

## Summary

The discussion centers around a bug fix in fork choice voting logic, specifically regarding the return value in an equality case. Potuz initially acknowledged a mistake where the `==` case should return `False` instead of `True`, noting that someone had already commented on the related PR. However, upon further reflection, Potuz realized that returning `True` could also work, explaining that their interop implementation previously used an analogous `True` approach that counts votes for both empty and full cases, then subtracts one when examining the parent since they don't use the concept of "pending."

jih2nn contributed by referencing another PR (#4800) aimed at refactoring the same code, suggesting that the current implementation has readability issues. They provided a summary of all suggested solutions in a comment on PR #4892, indicating that multiple approaches are being considered to improve both the correctness and clarity of this fork choice voting logic.

## Participants

- jih2nn
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-03T01:00:05.891000+00:00] potuz: Ah right, yeah this was a mistake in my part, the == case should return False
[2026-02-03T01:02:12.229000+00:00] potuz: Ah yeah <@520034910149410861> already added the comment in the PR
[2026-02-03T01:12:41.239000+00:00] potuz: Returning `True` in this case should also work I believe, which is funny
[2026-02-03T01:19:56.516000+00:00] potuz: Ah in fact our interop implementation a while ago used the analog of True here, counting the votes for both empty and full. Since we do not use the notion of *pending* we probably will continue using this mechanism of counting for both empty and full, but subtracting one when looking at the parent.
[2026-02-03T04:31:20.416000+00:00] jih2nn: There was another [PR](https://github.com/ethereum/consensus-specs/pull/4800) that wanted to refactor this code. It indicates that its readability has a room for improvement. I've summarized suggested solutions so far in [this comment](https://github.com/ethereum/consensus-specs/pull/4892#issuecomment-3838989561).
```

</details>
