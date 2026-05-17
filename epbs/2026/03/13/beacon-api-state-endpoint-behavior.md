# Beacon API State Endpoint Behavior

**Channel:** epbs | **Date:** 2026-03-13

## Summary

The discussion centers on the expected behavior of the Beacon API's debug state endpoint regarding which state version should be returned for different state identifiers. 0xunclebill asked whether the endpoint should always return the post-block state regardless of the state_id used.

Sproul proposed a nuanced approach where the returned state depends on the identifier type: justified and finalized states should return the pending state, requests by slot should return the canonical state for that slot, and requests by root should return whatever state the specified root references. However, potuz argued for consistently returning post-CL (consensus layer) states across all identifier types, referencing a GitHub issue comment with detailed justification.

Potuz emphasized that consistent post-CL state returns would simplify client implementation by avoiding the need for callers to add logic to determine which state version they're receiving. He noted this inconsistency is currently problematic for checkpoint sync implementations, though acknowledged the endpoint doesn't have many consumers overall, making the impact somewhat limited.

## Participants

- 0xunclebill
- potuz
- sproul

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-13T08:31:39.114000+00:00] 0xunclebill: should `GET /eth/v2/debug/beacon/states/{state_id}` always return the post-block state for all state ids?
[2026-03-13T08:32:31.361000+00:00] sproul: IMO justified and finalized should be the pending state, by slot should be whatever is canonical, and by root should be whatever the root points to
[2026-03-13T11:05:53.920000+00:00] potuz: I left a justification here for why I think post CL should be always preferred https://github.com/ethereum/beacon-APIs/issues/572#issuecomment-3991849618

And I think that is mostly right, avoiding the caller to have to add logic to find out. This is exactly what I'm facing now in checkpoint sync but not knowing what the server replies, and I can see the same happening if you request by slot. Anyway I don't think there's many consumers of this endpoint so not that important. But it is for checkpoint sync
```

</details>
