# Beacon state unmarshaling and consensus specs PR implementation

**Channel:** epbs | **Date:** 2026-04-18

## Summary

terencechain encountered an issue with unmarshaling beacon state data and asked if it was related to consensus specs PR #5113. jtraglia confirmed that the unmarshaling problem is indeed connected to that specific pull request.

The discussion concluded with terencechain identifying that they need to implement the changes from PR #5113 to resolve their beacon state unmarshaling issue. This represents a clear action item for terencechain to update their implementation based on the consensus specs changes.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-18T00:33:55.757000+00:00] terencechain: i cant even unmarhsal the beacon state for some reason, does this have? https://github.com/ethereum/consensus-specs/pull/5113 ?
[2026-04-18T00:34:43.355000+00:00] jtraglia: Yes, it does.
[2026-04-18T00:35:00.130000+00:00] terencechain: ah ok.. i need to implement that one 🙂
```

</details>
