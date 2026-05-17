# Geth getPayload execution errors and troubleshooting

**Channel:** epbs | **Date:** 2026-02-25

## Summary

stefanbratanov encountered a "JSON-RPC error: Internal error (-38005): Unsupported fork" when calling getPayload to prepare blocks for Geth. The error occurred despite using the recommended ethpandaops/geth:epbs-devnet-0 version, which barnabasbusa indicated should work with v3 calls.

The discussion revealed version compatibility issues around Engine API methods. stefanbratanov was initially using getPayloadV6, but barnabasbusa suggested switching to getPayloadV5 along with engine_newPayloadV5, noting the same error persisted. There was some confusion around the API versioning, with stefanbratanov explaining that engine_newPayloadV5 follows the new spec while others use older versions.

The final consensus was to use `engine_forkchoiceUpdatedV3`, `engine_newPayloadV5`, and `engine_getPayloadV5` for devnet-0 compatibility. stefanbratanov committed to raising a PR against the upstream repository later that day and shared a working branch at https://github.com/StefanBratanov/teku/tree/epbs-devnet-0 for testing purposes.

## Participants

- barnabasbusa
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-25T14:23:46.707000+00:00] stefanbratanov: Now, I am getting, just when i call get payload to prep the block for gloas, any idea?

```
2026-02-25 14:18:22.147 ERROR - Execution Client request failed. Make sure the Execution Client is online and can respond to requests.
java.lang.Exception: JSON-RPC error: Internal error (-38005): Unsupported fork
```
[2026-02-25T14:39:10.386000+00:00] stefanbratanov: any idea <@412614104222531604> ? do i have to set up amsterdamTime in geth or something
[2026-02-25T14:39:26.243000+00:00] barnabasbusa: it should just work with v3
[2026-02-25T14:39:39.397000+00:00] barnabasbusa: if you use ethpandaops/geth:epbs-devnet-0
[2026-02-25T14:39:49.558000+00:00] barnabasbusa: can you give me a teku branch I can test?
[2026-02-25T14:40:35.388000+00:00] stefanbratanov: that's for getPayload
[2026-02-25T14:40:57.411000+00:00] stefanbratanov: yeah, one second, I will create a branch
[2026-02-25T14:41:01.912000+00:00] barnabasbusa: are you using getPayloadV5?
[2026-02-25T14:43:05.929000+00:00] stefanbratanov: getPayloadV6
[2026-02-25T14:43:07.202000+00:00] stefanbratanov: https://github.com/StefanBratanov/teku/tree/epbs-devnet-0
[2026-02-25T14:43:49.336000+00:00] barnabasbusa: can you also do getPayloadV5?
[2026-02-25T14:43:56.797000+00:00] barnabasbusa: same issue as above
[2026-02-25T14:44:13.627000+00:00] stefanbratanov: but also newPayloadV5 or newPayloadV4?
[2026-02-25T14:44:55.773000+00:00] barnabasbusa: `engine_newPayloadV5` `getPayloadV5`
[2026-02-25T14:45:01.814000+00:00] barnabasbusa: things are becoming confusing 😂
[2026-02-25T14:45:25.399000+00:00] barnabasbusa: any way this could be sitting in the upstream repo?
[2026-02-25T14:45:43.674000+00:00] stefanbratanov: later today, I will raise a PR against upstream repo
[2026-02-25T14:46:26.044000+00:00] stefanbratanov: it's bit mixed engine_newPayloadV5 is new as per spec and others are the old ones, but ok whatever works for devnet-0 I guess
[2026-02-25T14:48:34.740000+00:00] barnabasbusa: `engine_forkchoiceUpdatedV3`
`engine_newPayloadV5`
`engine_getPayloadV5`
```

</details>
