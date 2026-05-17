# Amsterdam Engine API Implementation for GLOAS

**Channel:** epbs | **Date:** 2026-04-08

## Summary

Terence from the Prysm team reported that they are implementing the Amsterdam engine API methods for GLOAS in their main branch, specifically five new API method versions: engine_newPayloadV5, engine_getPayloadV6, engine_forkchoiceUpdatedV4, engine_getPayloadBodiesByHashV2, and engine_getPayloadBodiesByRangeV2.

The key question raised is whether the implementation should maintain the same payload and response structures as current versions (without blockAccessList or slotNumber changes) and only increment the method version numbers to ensure proper version negotiation for GLOAS. Terence is seeking confirmation from other execution client teams about their approach to ensure consistency across implementations.

This appears to be an open coordination question with no responses yet in the provided messages, indicating teams may still be aligning on the specific implementation details for the Amsterdam upgrade's engine API changes.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-08T15:05:46.850000+00:00] terencechain: Hello, on the prysm side we're implementing the Amsterdam engine API methods as defined in the execution-apis in our main branch

  ▎ - engine_newPayloadV5
  ▎ - engine_getPayloadV6
  ▎ - engine_forkchoiceUpdatedV4
  ▎ - engine_getPayloadBodiesByHashV2
  ▎ - engine_getPayloadBodiesByRangeV2

 Can we confirm that we're keeping the same payload/response structures for now (no blockAccessList or slotNumber changes yet) just bumping the method versions so the version negotiation is correct for gloas? is this what <@&1417820113981145228> you guys are doing as well?
```

</details>
