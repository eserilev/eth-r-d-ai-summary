# Anchor State Loading in Reference Tests

**Channel:** epbs | **Date:** 2026-02-24

## Summary

paulharris raised a concern about the anchor state loading mechanism in Ethereum reference tests, questioning whether it requires a permanent "hack" in all client implementations. The core issue is that when loading anchor state and anchor block data, clients cannot determine whether the state represents a block state or a payload state without having access to information from the parent block.

Currently, clients work around this ambiguity by attempting to find the state in payload states first, and if that fails, trying to fetch it as a block state. paulharris is seeking clarification on whether there's a better solution to this problem or if all clients are implementing the same workaround approach. The question remains open with no responses provided in this discussion excerpt.

## Participants

- .paulharris

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-24T20:08:06.845000+00:00] .paulharris: Im wondering if the anchor state concept in reference tests is going to forever require a hack in all clients, but maybe there's an answer i don't know... It would go for loading initial state as well, so hopefully we get a good answer...

When we load anchor state and anchor block, we can't determine if that state is a block state or a payload state, because to determine that we'd need information from the parent block, so currently we have to see if we can find it in the payload states otherwise try to fetch a block state - Do we have a good answer for this or does every client do the same thing?
```

</details>
