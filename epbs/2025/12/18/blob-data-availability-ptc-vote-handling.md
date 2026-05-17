# Blob Data Availability and PTC Vote Handling

**Channel:** epbs | **Date:** 2025-12-18

## Summary

The discussion centers on how `blob_data_available` in PTC (Payload Timeliness Committee) votes helps coordinate data availability decisions across Ethereum validators. When an attester hasn't seen some PTC votes, a proposer can provide those votes to heal the attester's view of the network state. The `blob_data_available` field serves as a mechanism for the PTC to signal data availability to subsequent proposers who may not be "supernodes" and lack direct attestation information about payload availability.

The protocol handles conflicts between PTC votes and actual data availability through a reorg mechanism. If a payload appears unavailable despite PTC votes indicating availability, attesters in the next slot won't support it, causing the block built on that payload to be reorganized out. Conversely, when a payload is present but PTC votes indicate blob data is unavailable, the proposer's response depends on their own knowledge - they can build on top if they know the data is available, or initiate a reorg if uncertain, with PTC votes guaranteeing their block remains canonical even if the data was actually available.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T11:51:12.400000+00:00] m.kalinin: So, in the case when an attester haven't seen some PTC votes, a proposer has a chance to heal that attester's view. Continuing on that, how is `blob_data_available` supposed to be used?
[2025-12-18T11:54:38.147000+00:00] potuz: this is because the next proposer may not be a supernode, and doesn't yet have attestation to know if the payload was available. So the PTC votes on their view of DA and if there are enough votes for blob_data_available then the payload must be available.
[2025-12-18T12:01:23.131000+00:00] m.kalinin: And then if the payload isn't available while it was according to PTC votes, attesters of the next slot won't support it and the block built on top of the payload will be re-orged out. Btw, what proposer is supposed to do if payload is present but blob data aren't available according to the PTC view?
[2025-12-18T15:17:29.606000+00:00] potuz: depends on the proposer, if the proposer knows that the data is available, then it can build on top of the payload. If it doesn't then it should reorg the payload, the PTC vote will guarantee that his block is canonical even if the data was indeed available
```

</details>
