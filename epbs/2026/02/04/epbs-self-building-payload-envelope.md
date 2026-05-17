# Payload envelope signature handling in local block building

**Channel:** epbs | **Date:** 2026-02-04

## Summary

0xunclebill initially questioned whether payload envelope signatures should use `bls.G2_POINT_AT_INFINITY` (infinity point) in local block building cases, similar to how bid signatures are handled. They also noted that documentation around payload envelope construction for local building scenarios seems incomplete and offered to submit a PR for clarification.

nc1234 clarified the key distinction: while bid signatures can use the infinity point because the proposer already signs the block containing the bid, execution payload envelopes are broadcast separately and therefore require proper proposer signatures to prevent forgery. They referenced the `verify_execution_payload_envelope_signature` function which performs signature verification even for self-built payloads.

The discussion resolved 0xunclebill's confusion about the different signature handling requirements between bids and payload envelopes, though the potential documentation gap they mentioned remains unaddressed.

## Participants

- 0xunclebill
- nc1234

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-04T00:44:43.320000+00:00] 0xunclebill: in the local building case, the bid signature should be `bls.G2_POINT_AT_INFINITY`. I assume the same should be for the payload envelope?
[2026-02-04T00:44:52.385000+00:00] 0xunclebill: Does anyone else feel that theres a bit of context missing for payload envelope construction in the local block building case? I can open a PR if that would be helpful
[2026-02-04T00:47:16.892000+00:00] nc1234: No, the reason why bid signature can be G2 is because the proposer already signs the block which includes the bid. Execution payload envelope is broadcasted separately so proposer needs to sign it or it can be forged.
[2026-02-04T00:47:51.245000+00:00] nc1234: You can see `verify_execution_payload_envelope_signature` has signature verification for self build
[2026-02-04T00:55:10.561000+00:00] 0xunclebill: ohh okay, got it
[2026-02-04T00:55:12.313000+00:00] 0xunclebill: thanks
```

</details>
