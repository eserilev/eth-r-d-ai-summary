# Status v2 messages post ePBS fork choice behavior

**Channel:** epbs | **Date:** 2026-01-16

## Summary

0xunclebill raised a potential issue with Status v2 messages after the ePBS (enshrined Proposer-Builder Separation) implementation. The core problem is that ePBS changes when beacon blocks are imported to fork choice relative to Data Availability (DA) checks. Pre-ePBS, blocks only entered fork choice after passing DA checks, meaning a node's `head_slot` guaranteed column availability. Post-ePBS, blocks are imported before DA checks complete, creating a gap where a node's advertised `head_slot` may not have completed column custody yet.

This timing change affects the reliability of Status v2 messages for peer discovery and data availability. While `earliest_available_slot` can signal column availability from the start of the DA window, the guaranteed custody range becomes `[start of DA window, head_slot - 1]` rather than including the current head slot. The discussion concludes with 0xunclebill suggesting a potential Status v3 protocol with additional fields like `head_payload_slot` to address this discrepancy, though this remains an open question without further discussion or consensus from other participants.

## Participants

- 0xunclebill

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-16T23:33:08.158000+00:00] 0xunclebill: Wanted to talk through status v2 messages post ePBS

Currently, when `earliest_available_slot` is set to a slot before the DA window, it signals that the node can serve columns from the start of the DA window through its current `head_slot`.
However, ePBS changes the fork choice import behavior:

Pre-ePBS: Beacon blocks are only imported to fork choice after passing DA checks, so `head_slot` implies column availability.
Post-ePBS: Beacon blocks are imported to fork choice before completing DA checks. This means:

- A node's Status v2 message will advertise `head_slot` as its latest fork choice head
- But the node may not yet custody columns for `head_slot` (DA checks still pending)
- Even with `earliest_available_slot` set to before the DA window, the guaranteed column custody range is only: `[start of DA window, head_slot - 1]`

Thinking out loud here, I'm wondering if we need a status v3 with potentially additional fields, like `head_payload_slot`?
```

</details>
