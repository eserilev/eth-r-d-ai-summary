# EL New Payload Parent Block Handling

**Channel:** epbs | **Date:** 2026-04-11

## Summary

Potuz raised a technical question about Execution Layer (EL) behavior when receiving new payloads that reference unknown parent blocks. They specifically asked whether the EL returns a "syncing" status in this scenario and whether the EL automatically attempts to request the missing parent block on its own.

This appears to be an open question about the expected behavior and implementation details of the Engine API's newPayload method when dealing with missing parent block dependencies. No responses or answers were provided in the discussion thread.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-11T22:03:43.582000+00:00] potuz: What happens in the EL if we send a new payloads for which they don't have the parent, do they return syncing? Do they request the parent on their own?
```

</details>
