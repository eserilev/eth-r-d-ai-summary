# PR Coordination for Next Devnet

**Channel:** epbs | **Date:** 2026-05-14

## Summary

Potuz is coordinating the merge of 4 PRs needed for the next devnet, specifically PRs #5236, #5212, #5210, and #5186, with all others depending on #5186. These PRs were agreed upon during ACDC and jtraglia confirmed they should work towards getting them merged. 

There are some bugs that need fixing in PR #5186, particularly addressing Jihoon's comments and updating Heze. Potuz noted that Nico has already fixed several issues in that branch and committed to cleaning up all 4 PRs today after his lecture. The team agreed to proceed with the cleanup work once the dependencies are resolved.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-14T14:39:27.381000+00:00] potuz: <@592004585506340885> for the next devnet then we want 4 of my PRs in https://github.com/ethereum/consensus-specs/pull/5236 seems to be clean 
https://github.com/ethereum/consensus-specs/pull/5212 
https://github.com/ethereum/consensus-specs/pull/5210
is what was agreed on ACDC , can I start working on the last one to clean it?

And they all depend on https://github.com/ethereum/consensus-specs/pull/5186
[2026-05-14T14:40:13.588000+00:00] jtraglia: Yes, let's work towards getting these merged.
[2026-05-14T14:40:58.857000+00:00] potuz: ok, so the last one is the most inminent one, will try to fix tests after lecture today
[2026-05-14T14:52:06.483000+00:00] jtraglia: In that PR there are bugs that need to be fixed (ie Jihoon's comments).
[2026-05-14T14:53:13.924000+00:00] potuz: I only see one right? https://github.com/ethereum/consensus-specs/pull/5186#discussion_r3197754113
[2026-05-14T14:53:52.031000+00:00] jtraglia: But you need to update Heze too.
[2026-05-14T14:53:54.568000+00:00] potuz: Nico fixed a bunch of things in that branch, I'll work on it when it's agreed
[2026-05-14T14:54:01.869000+00:00] jtraglia: Sounds good.
[2026-05-14T14:54:06.148000+00:00] potuz: since it seems it was agreed I'll clean the 4 today
```

</details>
