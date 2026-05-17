# Reorg Handling and Bid Acceptance Edge Cases

**Channel:** epbs | **Date:** 2026-04-15

## Summary

**0xunclebill** raised a potential edge case where during a blockchain reorganization, a node might not have any valid bids to accept because it had previously ignored bids that didn't match its initial view of the chain head. They noted this could become more critical if local block building is eventually deprecated, and proposed a solution involving a reprocess queue for non-canonical bids that could be reconsidered after a reorg.

**potuz** disagreed that this edge case needs to be addressed, arguing that since bids arrive for both the current and next slots, some bids should naturally match the current head view after a reorg. They also pointed out that builders are likely already sending different bids for different potential chain heads, similar to their current practices, which would naturally provide coverage for reorg scenarios.

The discussion appears to conclude with disagreement on whether this edge case requires special handling, with no clear consensus reached on implementing the proposed reprocess queue solution.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-15T01:15:16.200000+00:00] 0xunclebill: In the case of a reorg, I think there could be a situation where a the node doesnt have any bids to accept (since it had initially ignored bids that didnt initially meet its view of head). Probably not super important now, but if we want to eventually deprecate local block building we'll need to handle this edge case I think 

Our plan right now is to send non-canonical bids to a reprocess queue and in the case of re-org, process bids that now meet our new view of head
[2026-04-15T01:17:32.564000+00:00] potuz: I think this is not necessary to address, bids will come for next and current slot
[2026-04-15T01:18:16.061000+00:00] potuz: Thus some bids must match the current head view
[2026-04-15T01:18:52.437000+00:00] potuz: Also it's very likely builders themselves will be sending different bids for different heads
[2026-04-15T01:19:09.016000+00:00] potuz: As they currently build on top of different heads
```

</details>
