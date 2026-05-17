# Engine API Payload Bodies Retrieval

**Channel:** epbs | **Date:** 2026-02-23

## Summary

Stefan Bratanov raised a question about whether any client teams are using the `engine_getPayloadBodiesByHashV2` API method to retrieve transactions for finalized slots in order to reconstruct envelopes for serving over RPC. The specific use case involves storing only envelope metadata in the database while downloading the bulk transaction data from the Execution Layer (EL) when needed.

This appears to be an open question seeking input from client teams about their implementation approaches for payload body retrieval and storage optimization strategies. No responses or conclusions were provided in the discussion thread.

## Participants

- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-23T15:53:21.596000+00:00] stefanbratanov: A bit separate maybe from discussion, but do any client teams use `engine_getPayloadBodiesByHashV2` to retrieve transactions for finalized slots to reconstruct an envelope to serve over RPC, if so, I suppose you still store some metadata of the envelope on the DB but leave the bulk of the size to be downloaded from the EL
```

</details>
