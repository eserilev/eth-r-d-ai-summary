# Amsterdam Engine API Implementation for Prysm

---

## 2026-04-08 (epbs)

Terence from the Prysm team announced they are implementing Amsterdam engine API methods on their main branch, including five new versioned methods: `engine_newPayloadV5`, `engine_getPayloadV6`, `engine_forkchoiceUpdatedV4`, `engine_getPayloadBodiesByHashV2`, and `engine_getPayloadBodiesByRangeV2`.

The key question raised is whether the implementation should maintain the same payload and response structures as previous versions (without `blockAccessList` or `slotNumber` changes) and only bump the method versions to ensure proper version negotiation for "gloas". Terence is seeking confirmation from other client teams about this approach and asking if they are following the same implementation strategy.

No responses or consensus were captured in this discussion thread, leaving the coordination question open for other client teams to address.

**Participants:** terencechain

<details>
<summary>Raw messages</summary>

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

