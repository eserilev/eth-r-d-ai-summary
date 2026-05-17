# Checkpoint Sync Implementation Methods

**Channel:** epbs | **Date:** 2026-03-30

## Summary

Stefan asked about implementation approaches for checkpoint sync, specifically whether to request envelopes by root from peers or use checkpoint sync URLs with the Beacon API (noting that not all providers may support the latter approach).

Potuz responded that their implementation uses a range-based request strategy, requesting payloads from a given slot and blocks from slot+1. This appears to be their solution for the checkpoint sync implementation question, though the discussion remains brief without further elaboration on the trade-offs between different approaches.

## Participants

- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-30T16:10:39.629000+00:00] stefanbratanov: <@755590043632140352> how did you guys implement checkpoint sync, do you ask a peer for the envelope by root or use the checkpoint sync url and query the Beacon API (which I presume not every provider would support that)
[2026-03-30T16:13:22.346000+00:00] potuz: we request by range, request payloads from slot and blocks from slot+1
```

</details>
