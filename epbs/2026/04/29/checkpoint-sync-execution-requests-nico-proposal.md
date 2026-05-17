# Execution requests root in bid for checkpoint sync

**Channel:** epbs | **Date:** 2026-04-29

## Summary

The discussion centers on the technical rationale for including `execution_requests_root` in the bid structure for checkpoint sync functionality. Potuz explained that this inclusion allows validators to verify that execution requests in a block match those committed in the payload without needing to access the actual payload data. This design enables the beacon chain state transition function (STF) to operate independently of payload availability.

The primary motivation is to optimize checkpoint sync by eliminating the need to request previous payload data when starting synchronization - validators can begin syncing immediately upon receiving the state. Arnetheduck noted this relates to "top-up sync under ePBS" (enshrined Proposer-Builder Separation), indicating this is part of broader protocol improvements for the ePBS implementation.

## Participants

- arnetheduck
- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-29T04:47:24.967000+00:00] m.kalinin: why do we put `execution_requests_root` into the `bid`?
[2026-04-29T05:27:23.346000+00:00] potuz: To check that the requests in the block match those committed in the Payload, without having the Payload
[2026-04-29T05:31:32.610000+00:00] m.kalinin: to be able to run beacon chain STF without a payload, i see
[2026-04-29T05:39:25.936000+00:00] potuz: There point is that on checkpoint sync you get the state and we wanted to avoid requesting the previous payload to start syncing
[2026-04-29T06:26:59.192000+00:00] arnetheduck: Top-up sync under ePBS
```

</details>
