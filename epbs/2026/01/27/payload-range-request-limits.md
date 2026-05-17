# Payload by range request limits

**Channel:** epbs | **Date:** 2026-01-27

## Summary

.paulharris expressed surprise at the large limit for payload by range requests, noting that `max_request_payloads` is set to 128, which allows requesting a very wide range of payloads.

potuz provided context for this seemingly high limit, explaining that the number originates from Geth's implementation, which maintains 128 payloads that can potentially be reorganized. This suggests the limit is designed to accommodate the maximum number of payloads that might need to be accessed during blockchain reorganization events.

The discussion appears to be informational, with no decisions made or open questions raised - it was simply clarifying the rationale behind the current parameter value.

## Participants

- .paulharris
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T01:29:33.479000+00:00] .paulharris: just looking at the payload by range request, and max_request_payloads is 128.... kinda surprising we can request such a huge range?
[2026-01-27T01:30:05.409000+00:00] potuz: I believe this number comes from geth's that keeps these many payloads that can reorg
```

</details>
