# Consensus Specs PTC Implementation

**Channel:** epbs | **Date:** 2026-03-23

## Summary

The discussion centers around a consensus specs pull request (#4979) for PTC (Proposer-Target-Committee) implementation. potuz requested review of the latest commit before proceeding with test fixes, while jtraglia made several changes including renaming `ptc_lookbehind` to `ptc_window`, refactoring `process_ptc_window`, and fixing tests. 

A key debate emerged over whether to support `MIN_SEED_LOOKAHEAD` future epochs or hardcode the value to 1 (supporting only previous, current, and next epochs). jtraglia initially preferred hardcoding and suggested using a fixed 3-epoch window structure, but potuz argued for maintaining `MIN_SEED_LOOKAHEAD` support to ensure consistency across duty helpers and easier reasoning about proposals/PTC operations, while noting that removing `MIN_SEED_LOOKAHEAD` entirely would be a separate discussion.

After consideration, jtraglia ultimately reverted his changes and restored `MIN_SEED_LOOKAHEAD` usage, reaching consensus with potuz's approach. The implementation now maintains the flexible epoch lookahead system rather than a hardcoded 3-epoch window.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-23T15:49:39.156000+00:00] potuz: <@592004585506340885> could you take a look at the last commit here? https://github.com/ethereum/consensus-specs/pull/4979 I don't want to start fixing tests if you'll change the core logic right away 🙂
[2026-03-23T17:53:18.830000+00:00] jtraglia: Do we want to support `MIN_SEED_LOOKAHEAD` future epochs? I'm thinking we should just hardcode this to 1. So prev, curr, next.
[2026-03-23T17:54:58.093000+00:00] jtraglia: I pushed a few changes. Fixed tests, renamed `ptc_lookbehind` to `ptc_window` (like <@363800010518822915> suggested), and refactored `process_ptc_window`.
[2026-03-23T17:55:42.524000+00:00] jtraglia: Before & now, if `MIN_SEED_LOOKAHEAD` is not 1, there will be issues.
[2026-03-23T17:59:27.562000+00:00] jtraglia: I would prefer we do:
```
ptc_window: Vector[Vector[ValidatorIndex, PTC_SIZE], 3 * SLOTS_PER_EPOCH]
```
[2026-03-23T18:43:26.104000+00:00] potuz: We have to support MIN_SEED_LOOKAHEAD IMO, there's no point of keeping it without supporting it across helpers for duties, it makes it easier to reason about proposals/ptc, etc. I also think we should remove MIN_SEED_LOOKAHEAD anyway and just hardcode everything to 1, but that's a different story
[2026-03-23T19:51:36.878000+00:00] jtraglia: Hmm let me think about this some more & maybe revert the changes I made.
[2026-03-23T20:31:18.728000+00:00] jtraglia: <@755590043632140352> thanks for your message. I've reverted my change. We're using `MIN_SEED_LOOKAHEAD` again.
```

</details>
