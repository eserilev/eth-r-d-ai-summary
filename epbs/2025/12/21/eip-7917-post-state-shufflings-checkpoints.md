# EIP-7917 Post State Shufflings and Checkpoints

**Channel:** epbs | **Date:** 2025-12-21

## Summary

**potuz** discusses their prior analysis of EIP-7917, confirming they had previously investigated the safety of using either post state to obtain correct shufflings and determined it was safe. They note that checkpoints exhibit similar ill-defined behavior on Gloas and suggest a potential solution: properly defining the latest post state as being "full" when the payload is present, which would establish a total ordering on post states in the canonical chain.

While **potuz** had previously concluded that specifying this wasn't critical since only the block root (not the post state itself) is actually used, they acknowledge the current lack of proper specification and documentation. They express willingness to support efforts to formalize and document this behavior if others want to pursue it, despite their earlier decision to avoid adding what they saw as unnecessary notation and complexity.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-21T14:41:09.871000+00:00] potuz: I had looked into this before EIP-7917 and convinced myself that it was safe to use either post state to get the right shufflings. Doubt anything changed here. Checkpoints also have a similar ill-defined behavior on Gloas. We could define properly the latest post state as being full if the payload was present. That is we can always set a total ordering on post states in the canonical chain. I had convinced myself that it didn't matter much since the post state is not really used and only the block root was enough, didn't want to add the extra notation and clutter that this would add, but I'm not against if someone wants to specify and document this
```

</details>
