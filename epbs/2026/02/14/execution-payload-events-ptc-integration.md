# Execution Payload Events and PTC Integration

**Channel:** epbs | **Date:** 2026-02-14

## Summary

The discussion centers around the `execution_payload_available` event and its integration with PTC (Payload Timeliness Committee). The event is designed to be emitted when both execution payload and blob data are available for payload attestation, specifically intended for PTC usage to allow voting as soon as data is ready rather than waiting until the 4-second deadline. However, there's confusion about the event's naming and scope, with suggestions for alternatives like `new_signed_payload_envelope` or `execution_payload_and_blobs_available`.

A key technical distinction emerges between availability and validity requirements. PTC only needs data availability to cast votes (minimum of payload + blob availability or deadline), while Dora (likely a block explorer) wants to be notified as soon as payloads are available via API, even if invalid, since invalid payloads are orphaned later but should still be displayed. This creates different use case requirements for the same underlying functionality.

The discussion concludes that separate events may be needed: the existing `execution_payload_available` for PTC (availability-focused), and additional events like `execution_payload` and `execution_payload_gossip` to mirror existing block events for other consumers like Dora. The exact naming convention remains an open question, with participants acknowledging the current name could be misleading about when blob data must be present.

## Participants

- nflaig
- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-14T10:03:54.268000+00:00] nflaig: so `execution_payload_available` is only emitted if payload + data is available (the idea is that this will be used by PTC similar to how attesters currently use the `head` event to cast their vote as soon as head is updated and not wait until 4 seconds), do we want a `execution_payload` (and `execution_payload_gossip`) mirroring the block events?
[2026-02-14T10:04:21.393000+00:00] nflaig: also could think of a better name for `execution_payload_available`
[2026-02-14T13:35:57.591000+00:00] potuz: I don't think this is right, PTC does not need the payload to be valid, while Dora definitely wants the payload to be validated to report it, the event should be emmitted after validation I think
[2026-02-14T13:59:01.397000+00:00] nflaig: so which part is not right? `execution_payload_available` is specifically that and meant to be used by ptc only, as per description
> The node has verified that the execution payload and blobs for a block are available and ready for payload attestation
it's only about availability, not validity
[2026-02-14T13:59:10.920000+00:00] nflaig: hence I am suggesting we need separate events for dora
[2026-02-14T13:59:35.421000+00:00] potuz: so you guys added an event specifically to be used by PTC?
[2026-02-14T13:59:55.211000+00:00] potuz: if that's the intended use, that name is a bit misleading
[2026-02-14T14:00:12.748000+00:00] nflaig: ^^ open for suggestions
[2026-02-14T14:00:49.885000+00:00] potuz: `new_signed_payload_envelope` or similar?
[2026-02-14T14:01:13.399000+00:00] nflaig: and general question, does the PTC need it at all or are we fine with casting the vote at BPS deadline?
[2026-02-14T14:01:25.447000+00:00] potuz: The problem is that this endpoint should **only** return once the blob data is actually available
[2026-02-14T14:01:55.229000+00:00] nflaig: `execution_payload_and_blobs_available` bit verbose
[2026-02-14T14:01:55.935000+00:00] potuz: I think it's best to cast the vote at the minimum between payload *AND* data is available or the deadline
[2026-02-14T14:03:34.388000+00:00] potuz: I guess for Dora it's fine to use the same endpoint and just call with a delay
[2026-02-14T14:03:46.631000+00:00] nflaig: this is definitely wrong because it reads like it should ~~only~~ already emit when only payload envelope is there
[2026-02-14T14:04:39.838000+00:00] potuz: yeah I suck at names will let you guys decide...
[2026-02-14T15:58:43.188000+00:00] pk910: Dora doesn't need to wait for a validity check of the payload or the blob sidecars.
Dora likes being notified as soon as the payload is available via the api. Invalid payloads are orphaned later on, but the payload should still be displayed
[2026-02-14T18:12:31.968000+00:00] nflaig: > soon as the payload is available 
I believe we would need a separate event as noted above `execution_payload` and `execution_payload_gossip`
[2026-02-14T18:12:59.080000+00:00] nflaig: `execution_payload` would be the"available via the api" case
```

</details>
