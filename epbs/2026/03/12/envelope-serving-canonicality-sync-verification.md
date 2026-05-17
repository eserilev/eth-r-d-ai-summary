# Envelope serving canonicality and sync verification implementation

**Channel:** epbs | **Date:** 2026-03-12

## Summary

The discussion centered on implementation details for envelope serving in Ethereum's sync protocol, specifically around canonicality checks and verification strategies. 0xunclebill raised concerns about the awkwardness of serving envelopes by root without checking canonicality, as it requires fetching child block roots and comparing bids against payloads. Potuz clarified that envelopes must be served even if non-canonical (similar to blocks by root) to enable syncing alternative branches, which 0xunclebill accepted.

For envelope range requests, the conversation revealed different approaches for finalized versus unfinalized data. Potuz explained that finalized data can be served with trust in canonicality, while unfinalized data benefits from forkchoice information that provides O(1) canonicality checking. The handling of skipped slots in range requests was identified as another edge case requiring iteration to find child blocks.

Potuz outlined a sync implementation strategy involving unconditional downscoring of peers that return inconsistent payload lists (by hash dependency), with additional penalties when blocks and payloads from the same peer are inconsistent with bids. The group agreed these implementation details should be included in the specification since they affect peer penalization behavior.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-12T08:04:38.230000+00:00] 0xunclebill: for envelope by root requests is it ok to serve an envelope without checking that its canonical? its a bit awkward that for a given block root, we need to figure out it child block root and then compare the childs bid against the payload before serving it.
[2026-03-12T08:05:10.196000+00:00] 0xunclebill: for envelopes by range this is much less awkward to deal with since we are just iterating over a range of slots
[2026-03-12T08:13:09.260000+00:00] 0xunclebill: also are details like this worth including in the spec? feels like it should since it affects how we'd penalize peers
[2026-03-12T09:31:25.952000+00:00] potuz: Just like block by root you must serve them even if they aren't canonical
[2026-03-12T09:32:28.821000+00:00] potuz: Otherwise we wouldn't be able to sync other branches
[2026-03-12T09:37:14.610000+00:00] 0xunclebill: yeah makes sense

btw how are you handling verifying the last requested slot in a envelope by range request? you fetch its child block i assume? and in the case where there are skipped slots you just iterate until you find its child block? that edge case is a bit akward for us too
[2026-03-12T09:55:56.011000+00:00] potuz: I haven't seen the RPC code in Prysm but for finalized data we should just simply serve it and trust that they are canonical (I know this isn't working cause I'm pretty sure we aren't pruning yet, so I think we check explicitly with child blocks). For unfinalized data forkchoice has this information for us since every node has a pointer to the best node, hence checking if something is canonical is O(1). 

But anyway serving unfinalized ranges should be trivial just give the best child until you find head or a higher slot than the requested slot
[2026-03-12T12:44:29.051000+00:00] potuz: Fwiw <@358120958726373381> I'm implementing sync from scratch right now and this is what I'll do. We don't necessarily receive blocks and Payloads from the same peer when requesting by range. So I'll unconditionally downscore peers that do not return a self consistent list of payloads (by hash dependency). In addition if the peer is the same as the peer that sent us the blocks I'll downscore if the Payloads are not consistent with the bids
```

</details>
