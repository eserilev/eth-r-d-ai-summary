# Fork Choice Function Updates Implementation

**Channel:** epbs | **Date:** 2026-01-05

## Summary

The discussion centers around updating fork choice functions `is_head_weak` and `is_parent_strong` to properly integrate with an updated `get_weight` function. jtraglia initially requested these updates and noted that `is_head_weak` had already been addressed in a "finishing touches" PR, but `is_parent_strong` still needed work.

fradamt implemented the requested changes to `is_parent_strong`, making a key modification to change the function input from `parent_root` to `root` across all specifications for consistency. This change was necessary because the `root` parameter is required to derive the `parent_node` (including `payload_status`) needed for the `get_weight`/`get_attestation_weight` functions. Additionally, fradamt used `get_attestation_weight` in `is_parent_strong` to avoid proposer boost influence and prevent infinite loops, similar to how it was implemented in `is_head_weak`.

The changes were approved by jtraglia, who indicated plans to merge the PR soon and requested additional reviews from other team members for PR #4807. The discussion concluded with the technical approach being accepted and moving toward final review and merge.

## Participants

- fradamt
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-05T18:37:21.358000+00:00] jtraglia: <@520034910149410861> can you submit a PR which updates `is_head_weak` and `is_parent_strong` to properly use the updated `get_weight` function?
[2026-01-05T18:38:09.776000+00:00] jtraglia: It's not obvious to me how to do this.
[2026-01-05T18:52:23.929000+00:00] jtraglia: Ah I see that you've updated `is_head_weak` in your "finishing touches" PR 🙂
[2026-01-05T18:52:25.239000+00:00] jtraglia: 
[2026-01-05T18:52:35.701000+00:00] jtraglia: We should update `is_parent_strong` there too.
[2026-01-05T20:08:30.784000+00:00] fradamt: I updated `is_parent_strong` there, had to change the input from `parent_root` to `root` because you need `root` to derive the `parent_node`, inclusive of `payload_status`, which is what we need for `get_weight`/`get_attestation_weight` . I decided to change the input to `root` everywhere, in other specs as well, to just have one consistent way of using the function throughout.

A note about `get_attestation_weight`: it was introduced to avoid an infinite loop in `is_head_weak`, since `should_apply_proposer_boost` in `get_weight` calls `is_head_weak` again. I think it makes sense to also use it in`is_parent_strong` to avoid proposer boot influencing the result.
[2026-01-05T20:09:12.814000+00:00] fradamt: Is this ok, or should I make a separate PR with these in case we want to merge them before the whole fork-choce PR?
[2026-01-05T20:36:21.309000+00:00] jtraglia: This looks good, thank you! I intend to merge this PR soon.
[2026-01-05T20:51:24.415000+00:00] jtraglia: <@363800010518822915> <@755590043632140352> could you give [4807](https://github.com/ethereum/consensus-specs/pull/4807) another review?
```

</details>
