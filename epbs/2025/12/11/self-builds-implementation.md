# Self-builds implementation changes

**Channel:** epbs | **Date:** 2025-12-11

## Summary

The discussion focuses on a needed implementation change for handling self-builds in the Ethereum protocol. jtraglia identifies that the current logic using `builder_index == block.proposer_index` to check for self-builds is no longer appropriate and needs to be updated.

As a solution, jtraglia proposes replacing the current check with `builder_index == BuilderIndex(UINT64_MAX)` to properly identify self-build scenarios. This appears to be a straightforward technical fix, though no explicit consensus or further discussion is captured in these messages.

## Participants

- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-11T22:19:10.487000+00:00] jtraglia: Gotta figure out self-builds now. The `builder_index == block.proposer_index` check doesn't make sense anymore.
[2025-12-11T22:23:13.506000+00:00] jtraglia: Pretty sure we can just do `builder_index == BuilderIndex(UINT64_MAX)` instead
```

</details>
