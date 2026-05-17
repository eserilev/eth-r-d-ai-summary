# Blob sidecars documentation fix in validator.md

**Channel:** epbs | **Date:** 2026-01-21

## Summary

0xunclebill identified an inaccuracy in the Ethereum consensus specs documentation regarding blob sidecars. The current documentation in `validator.md` states that "blob sidecars are no longer broadcast by the validator, and thus their construction is not necessary," but this appears to be incorrect for local block building scenarios where validators still need to construct blob sidecars.

jtraglia confirmed this issue had been previously discussed in PR #4772 and acknowledged the documentation was inaccurate. As a resolution, jtraglia committed to rewriting the problematic paragraph and suggested potentially removing it entirely. The discussion concluded with jtraglia creating PR #4853 to address the documentation fix.

## Participants

- 0xunclebill
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-21T19:58:34.092000+00:00] 0xunclebill: https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/validator.md#blob-sidecars

> The blob sidecars are no longer broadcast by the validator, and thus their construction is not necessary

this doesn't seem to be true in the case of local block building?
[2026-01-21T20:21:30.623000+00:00] jtraglia: Yeah that's a bit inaccurate. We brought this up in this PR: https://github.com/ethereum/consensus-specs/pull/4772/changes#r2590664642
[2026-01-21T20:22:09.358000+00:00] jtraglia: Later today, I'll re-write that paragraph.
[2026-01-21T20:23:06.608000+00:00] jtraglia: Maybe we should just remove that paragraph.
[2026-01-21T20:52:43.654000+00:00] jtraglia: https://github.com/ethereum/consensus-specs/pull/4853
```

</details>
