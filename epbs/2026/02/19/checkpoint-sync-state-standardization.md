# Checkpoint Sync State Standardization Discussion

**Channel:** epbs | **Date:** 2026-02-19

## Summary

stefanbratanov initiated a discussion about standardizing what the `eth/v2/debug/beacon/states/finalized` endpoint returns, specifically proposing it should return the state after applying the execution payload state to avoid requiring changes to checkpoint syncing logic. nc1234 noted this concern was raised previously and remains under discussion.

The participants showed mixed agreement on the approach. 0xunclebill supported serving the "best" state at a given slot, meaning the post-payload state when fork choice indicates a payload exists at that slot. However, potuz disagreed, arguing it should return the post-state of the block since checkpoints can be empty blocks, and noted that checkpoint sync logic will need to be rethought as it would require getting the payload for that post-state.

The discussion highlights an ongoing standardization issue with no clear resolution, and potuz's comments suggest that broader changes to checkpoint sync methodology may be necessary regardless of how this specific endpoint behavior is standardized.

## Participants

- 0xunclebill
- nc1234
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-19T17:01:18.587000+00:00] stefanbratanov: <@358120958726373381> what does lighthouse return for `eth/v2/debug/beacon/states/finalized` ? I think we should standardize this to return the state after applying the execution payload state, otherwise need to make changes to the checkpoint syncing logic
[2026-02-19T18:53:50.803000+00:00] nc1234: I think I raised this concern a while ago https://discord.com/channels/595666850260713488/874767108809031740/1466295421692280946 , I think it is still in discussion.
[2026-02-19T19:24:23.761000+00:00] 0xunclebill: I do agree that we should serve the “best” state at a given slot. if fork choice indicates there’s a payload at a given slot, we should return the post payload state
[2026-02-19T21:12:03.266000+00:00] potuz: I do not think so, it should be the post-state of the block since the checkpoint can be an empty block
[2026-02-19T21:12:51.532000+00:00] potuz: But yeah, checkpoint sync should be rethought
[2026-02-19T21:13:04.325000+00:00] potuz: as it will require getting the payload for that post-state
```

</details>
