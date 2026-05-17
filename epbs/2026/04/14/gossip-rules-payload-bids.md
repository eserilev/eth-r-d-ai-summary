# Gossip Rules for Payload Bids

**Channel:** epbs | **Date:** 2026-04-14

## Summary

pawandhananjay raised a question about which state should be used when validating gossip rules for payload bids, specifically for the `can_builder_cover_bid` function that checks if a builder has sufficient excess balance. They noted that as a proposer, only bids with `parent_block_root` matching the canonical head would be useful, and loading states corresponding to different parents could create a DoS risk without meaningful benefit.

potuz responded that many gossip rules suffer from similar underspecification regarding which state to reference, and suggested that the convention is to use the head state of the validating node for all such checks. This appears to align with pawandhananjay's intuition about using the canonical head state, though the discussion leaves the broader issue of gossip rule specification unresolved.

## Participants

- pawandhananjay
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-14T15:55:42.556000+00:00] pawandhananjay: Quick question about the gossip rules for payload bids. The spec says
> [IGNORE] bid.value is less or equal than the builder's excess balance -- i.e. can_builder_cover_bid(state, builder_index, amount) returns True.

What state are we using here? as a proposer, the only useful bids for me would be ones with `parent_block_root` as my canonical head, so would make sense to only check against that state. 
If we start loading states corresponding to the parent, then it opens us up to a dos risk for not much gain. How are other clients handling this?
[2026-04-14T15:56:35.172000+00:00] potuz: many gossip rules are underspecified in this same regard IMO, I take it that they all refer to the head state of the validating node
```

</details>
