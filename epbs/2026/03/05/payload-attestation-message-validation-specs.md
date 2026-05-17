# Payload attestations implementation status

**Channel:** epbs | **Date:** 2026-03-05

## Summary

terencechain inquired about the implementation status of payload attestations across Ethereum clients, specifically asking which clients have implemented them "end to end." The discussion revealed varying levels of implementation progress among different client teams.

stefanbratanov indicated their client has implemented payload attestations with fork choice processing, while tomi0x clarified that their team has implemented the feature but hasn't yet integrated it into fork choice. When terencechain defined "end to end" as including validator clients submitting payload attestations and proposers packing them into blocks, tomi0x confirmed their implementation meets these criteria.

terencechain concluded by stating they would work on implementing this feature for Prysm next, indicating this is an active area of development across the Ethereum client ecosystem with different teams at various stages of completion.

## Participants

- stefanbratanov
- terencechain
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T15:01:16.757000+00:00] terencechain: Any client implemented payload attestations end to end?
[2026-03-05T15:05:16.372000+00:00] stefanbratanov: apart from processing them in fork choice yeah
[2026-03-05T15:11:54.001000+00:00] tomi0x: Can you define “end to end”
[2026-03-05T15:12:11.634000+00:00] tomi0x: We don’t have it integrated in fork choice yet
[2026-03-05T15:12:21.292000+00:00] tomi0x: But we do have it implemented
[2026-03-05T15:26:45.254000+00:00] terencechain: - validator clients are submitting payload attestations
- proposers are packing payload attestation in the block
[2026-03-05T15:27:07.775000+00:00] terencechain: thanks, that's awesome, ill work on this next for prysm
[2026-03-05T15:29:10.249000+00:00] tomi0x: Yes, we do this
```

</details>
