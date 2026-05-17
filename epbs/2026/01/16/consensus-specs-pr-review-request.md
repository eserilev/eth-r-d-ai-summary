# PR submission and hash fixes

**Channel:** epbs | **Date:** 2026-01-16

## Summary

jtraglia submitted PR #4841 to the ethereum/consensus-specs repository, but potuz identified that it contains an incorrect hash implementation. The issue centers around the distinction between parent beacon block root and state.latest_block_hash - potuz clarified that a parent beacon block root should never be the state.latest_block_hash, and instead the parent block hash must be used.

jtraglia acknowledged the feedback and agreed to fix the implementation. The discussion suggests this was a timing issue where jtraglia proceeded with the PR submission before getting proper guidance from potuz, who noted he should have waited until potuz was available to provide input on the correct hash usage.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-16T00:39:43.975000+00:00] jtraglia: Made this PR: https://github.com/ethereum/consensus-specs/pull/4841
[2026-01-16T13:36:20.043000+00:00] potuz: I think it's still not the right hash, requested that change
[2026-01-16T13:41:54.495000+00:00] jtraglia: <@755590043632140352> I asked about this here 😅
[2026-01-16T13:42:29.744000+00:00] jtraglia: But yeah, will fix.
[2026-01-16T13:45:20.437000+00:00] potuz: yep, you should have waited until I was at the keyboard 🙂
[2026-01-16T13:45:47.994000+00:00] potuz: but again a parent beacon block root is never the state.latest_block_hash
[2026-01-16T13:45:59.854000+00:00] potuz: the parent block hash has to be that
```

</details>
