# Detailed Analysis of Validator Balance Edge Cases

**Channel:** epbs | **Date:** 2026-04-07

## Summary

Potuz completed a detailed analysis of validator balance edge cases and identified that divergence problems can only arise after 5 epochs with missing blocks. The analysis will be documented in an updated blog post once access issues are resolved.

A specific edge case was identified involving validator consolidation requests processed in the last payload of an epoch where the validator has more than 32.25 ETH in balance. In this scenario, the extra 0.25 ETH would be counted in the post-payload effective balance but not in the post-consensus layer block effective balance, creating a discrepancy. This case requires patching as an attacker could potentially exploit it by timing a balance top-up with last-payload inclusion of a consolidation request followed by an entire epoch of empty blocks.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-07T15:56:35.928000+00:00] potuz: Ok, I think I did a fully detailed analysis of the possible divergence and I think the only *problem* can really arise after 5 epochs with missing blocks
[2026-04-07T15:56:58.833000+00:00] potuz: Need to update my blog post, but I'm locked out of my own ssh server, can upload after I get back home
[2026-04-07T15:58:39.526000+00:00] potuz: There is only a single subtlety which is the weird case in which we consider a switch to consolidation validator in the last payload of the epoch, and that validator happened to have more than 32.25ETH in the balance, this is the **only** difference that can actually happen because those 0.25ETH would count on the post-payload effective balance and they will not count in the post-CL block effective balance
[2026-04-07T16:00:01.363000+00:00] potuz: So this case probably needs to be patched in case the attacker times a topup with a last payload inclusion of this consoludation request and manages to get an entire epoch worth of empty blocks right after
```

</details>
