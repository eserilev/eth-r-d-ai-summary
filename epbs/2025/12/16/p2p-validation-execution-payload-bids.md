# P2P validation for execution payload bids

**Channel:** epbs | **Date:** 2025-12-16

## Summary

The discussion centered on whether P2P validation for execution payload bids should enforce that `bid.parent_block_hash == state.latest_block_hash` and similar constraints for `bid.parent_block_root`. Bharath initially questioned whether it would be undesirable for these fields to reference random past blocks from the fork choice store, but Potuz clarified that such strict validation wouldn't be appropriate since the proposer's view of the head may differ from other nodes' current view.

Bharath then proposed a PR (#4793) specifying conditions that bids must fulfill before proposers include them in blocks, drawing an analogy to similar checks in the builder-specs. Potuz suggested making these checks optional, noting uncertainty about whether they need formal specification beyond ensuring bids pass `process_execution_payload_bid`. 

The participants agreed that while these validation checks would likely be implemented by clients when considering `SignedExecutionPayloadBid` messages, they aren't critical to specify formally in the consensus specs right now, though they would be "good to have." The discussion left open the question of whether to proceed with the optional specification approach.

## Participants

- bharath.vedartham
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-16T15:12:41.794000+00:00] bharath.vedartham: also had a question on this p2p validation for `execution_payload_bid`, do we want to enforce here that:
`bid.parent_block_hash == state.latest_block_hash` and similar for `bid.parent_block_root`
[2025-12-16T15:15:07.909000+00:00] bharath.vedartham: i would assume its undesirable for the `bid.parent_block_hash` and `bid.parent_block_root` to be some random blocks from the past in the fork choice store?
[2025-12-16T15:50:42.884000+00:00] potuz: no, the proposer's view of the head may be different than your current view of the head
[2025-12-16T16:04:21.149000+00:00] bharath.vedartham: unsure if this PR makes sense: https://github.com/ethereum/consensus-specs/pull/4793 i tried to specify the conditions that a bid must fulfill before the proposer tries to include it in the block.
[2025-12-16T16:06:41.626000+00:00] potuz: Can we make these optional? I am uncertain if these need to be specified, other than making sure that the bid will pass `process_execution_payload_bid`
[2025-12-16T16:17:18.887000+00:00] bharath.vedartham: i think we can. in my mind, these checks are only run by the proposer who is building the block for the given slot. it is for them to know whether the bid is valid to be included in the block. i guess client implementations will have these checks while considering a `SignedExecutionPayloadBid`
[2025-12-16T16:19:14.611000+00:00] bharath.vedartham: in my mind, its analogous to this check in the builder-specs: https://github.com/ethereum/builder-specs/blob/main/specs/bellatrix/validator.md?plain=1#L132  except we need to have it in the consensus-specs because `SignedExecutionPayloadBid` is defined in the consensus-specs
[2025-12-16T16:19:54.177000+00:00] bharath.vedartham: I dont see this as something critical to have right now, but seems like a good to have to me
```

</details>
