# Rollback tracking and beacon API endpoints

**Channel:** epbs | **Date:** 2026-05-02

## Summary

terencechain provided a side comment suggesting that people interested in tracking rollbacks could monitor the head event endpoint via the beacon API, which indicates whether blocks are empty or full. This approach would provide the same information that the consensus layer (CL) obtains when calling the forkchoiceUpdated (FCU) method and receiving data from the execution layer's endpoint.

This appears to be a suggestion for an alternative method to track rollback events using existing beacon API infrastructure rather than requiring new mechanisms.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-02T13:18:19.516000+00:00] terencechain: side comment, ppl that care about rollback could also track head event end point via beacon api that should indicates empty/full, this is the same as CL calling FCU and getting it via execution's end point
```

</details>
