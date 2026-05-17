# Lighthouse attestation index testing

**Channel:** epbs | **Date:** 2026-03-04

## Summary

0xunclebill is testing Lighthouse changes and seeking confirmation about which clients correctly handle attestation index behavior in a specific edge case scenario. The scenario involves when the block being attested to has a slot number less than the current slot, and the payload may or may not have been previously seen by the client.

Based on their testing, 0xunclebill found that both Lodestar and Prysm appear to handle this attestation index case correctly and is seeking verification from others in the channel. No responses or confirmations from other developers were provided in this discussion thread.

## Participants

- 0xunclebill

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T04:03:51.521000+00:00] 0xunclebill: im trying to test some lighthouse changes right now and wondering if theres a client that handles attestation index correctly when the block being attested to slot < current slot and the payload has or hasnt been seen
[2026-03-04T04:06:39.495000+00:00] 0xunclebill: it seems like lodestar and prysm are handling it correctly, just double checking
```

</details>
