# Gloas Forkchoice Tests and Attestation Bug

**Channel:** epbs | **Date:** 2026-02-19

## Summary

Potuz has fixed forkchoice tests in PR #4918 by modifying all attestations to target empty blocks and ensuring the complete forkchoice tree is empty post-Gloas activation. The changes make the PR ready for merging, though Potuz notes that extensive explicit Gloas forkchoice tests will still be needed in the future.

Additionally, Potuz highlighted an important edit in the PR description indicating that processing these specific attestations may actually represent a current bug in the implementation, suggesting there's an underlying issue beyond just the test fixes that needs attention.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-19T22:17:14.690000+00:00] potuz: <@592004585506340885> I fixed the tests in https://github.com/ethereum/consensus-specs/pull/4918 by making all attestations to be for empty blocks and then making the full forkchoice tree be empty post-gloas. We will definitely need extensive explicit Gloas forkchoice tests.
[2026-02-19T22:17:22.216000+00:00] potuz: But I hope that PR is mergeable now
[2026-02-19T22:17:53.774000+00:00] potuz: Tomorrow I am going back home so I won't have again the excuse of vacations, but for the record I hate you guys
[2026-02-19T22:18:53.802000+00:00] potuz: Please notice the EDIT in the description, I think it's actually a current bug to process these attestations.
```

</details>
