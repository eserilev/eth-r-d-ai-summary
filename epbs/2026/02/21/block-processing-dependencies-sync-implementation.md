# Block Processing Dependencies and Sync Implementation Issues

**Channel:** epbs | **Date:** 2026-02-21

## Summary

dapplion raised an implementation challenge with Ethereum sync, highlighting an awkward dependency chain when processing blocks. To process block N, they must first fetch block N, then fetch its parent block, then fetch the pre-state based on the parent information, and finally run the state transition. This extra dependency of needing the parent block information makes the sync implementation cumbersome.

potuz questioned whether this was specifically about forward sync and suggested that parent information should already be available in forkchoice during forward syncing. They asked whether forkchoice is being run during the forward sync process, implying this might resolve the dependency issue.

The discussion leaves open the question of whether the dependency problem is specific to certain sync modes and whether proper use of forkchoice could eliminate the need to separately fetch parent block information.

## Participants

- dapplion
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-21T20:45:10.057000+00:00] dapplion: I'm implementing sync and it's very annoying having to read another block to know which pre-state you need to process a block
[2026-02-21T20:45:57.729000+00:00] dapplion: Now to process a block you need to
- fetch block N
- fetch parent(block N)
- fetch pre-state for block N, based on  parent(block N)
- run state transition

This extra dependency is awakward
[2026-02-21T22:15:06.683000+00:00] potuz: Is this forward sync?
[2026-02-21T22:15:59.590000+00:00] potuz: Why wouldn't you have the parent information already in forkchoice in that case? You do not run forkchoice when forward syncing?
```

</details>
