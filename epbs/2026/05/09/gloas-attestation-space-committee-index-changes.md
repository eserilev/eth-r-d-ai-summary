# Phase 0 vs Gloas attestation scoring changes

**Channel:** epbs | **Date:** 2026-05-09

## Summary

m.kalinin identified two key changes in Gloas compared to Phase 0 attestation scoring: both `is_parent_strong` and `is_head_weak` functions now use `get_attestation_score` instead of `get_weight`, and `is_head_weak` now includes equivocations from the head slot committee in its calculations. They questioned why these changes aren't being applied to Phase 0, noting that the modifications don't appear to be related to ePBS (enshrined Proposer-Builder Separation).

potuz responded that these changes haven't been backported to Phase 0 because implementing equivocation counting is non-trivial and would cause existing implementations to fail both fork choice tests and spectests. This suggests the changes represent a significant implementation complexity that would break compatibility with current Phase 0 testing infrastructure.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-09T06:20:28.392000+00:00] m.kalinin: I am looking at `is_parent_strong` and `is_head_weak` mods in Gloas:
1) Both functions are switched to `get_attestation_score` as opposed to `get_weight` in Phase 0
2) `is_head_weak` now counts in equivocations from the head slot committee

I don't find this changes ePBS related. Q: why aren't they applied to Phase 0? cc <@755590043632140352> <@602753420033785856>
[2026-05-09T10:14:41.325000+00:00] potuz: I think the reason these changes weren't back ported is that no one would pass FC tests. The equivocation counting is not trivial to implement and we wouldn't pass spectests at all.
```

</details>
