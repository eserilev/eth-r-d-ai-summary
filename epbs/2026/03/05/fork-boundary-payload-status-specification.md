# Fork boundary payload status specification

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers on a specification issue with `get_parent_payload_status` in the Gloas fork choice spec, where the function unconditionally accesses `parent.body.signed_execution_payload_bid` to determine payload inclusion status, causing problems at fork boundaries. Hopinheimer reports implementing an explicit `PayloadStatus::Full` for parent blocks at fork boundaries and their ancestry in Lighthouse for semantic consistency, while potuz acknowledges they currently use "full sometimes and empty some others" in their implementation.

Potuz explains that the specification doesn't have a clear notion of transition states—it's designed to be either Fulu or Gloas with fork tests handling upgrades. While it would be possible to address this in the executable path, doing so in the markdown specification would be "ugly." The current approach intentionally uses "empty" in some paths where Fulu behavior would treat it as "full" to minimize changes to existing Fulu code paths, though hopinheimer suggests it would be beneficial to enforce consistent behavior in the specification.

## Participants

- hopinheimer
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T20:39:44.275000+00:00] hopinheimer: quick question here regarding the spec. `get_parent_payload_status` in the Gloas fork choice spec unconditionally accesses `parent.body.signed_execution_payload_bid` to determine whether the parent block's payload was included, which seems to break at fork boundary. I have explicitly considered the payload status to be PayloadStatus::Full for parent block at fork boundary as well as the ancestry in LH for semantic alignment.
[2026-03-05T20:41:29.665000+00:00] potuz: we use full sometimes and empty some others 🙂
[2026-03-05T20:42:22.595000+00:00] hopinheimer: wouldn't it nice to enforce it in the spec?
[2026-03-05T20:42:35.651000+00:00] potuz: the spec doesn't really have a notion of transition really
[2026-03-05T20:42:50.616000+00:00] potuz: it's either fulu or Gloas
[2026-03-05T20:42:59.068000+00:00] potuz: and then there's the fork tests for the upgrade
[2026-03-05T20:43:19.405000+00:00] potuz: you can do something in the executable path, but from the markdown it is a bit ugly
[2026-03-05T20:43:52.492000+00:00] potuz: I didn't want to use "full" unconditionally in that situation to touch the minimum possible the fulu paths
[2026-03-05T20:44:09.245000+00:00] potuz: so we use empty in some paths where the usual fulu behavior would be as if it were full
```

</details>
