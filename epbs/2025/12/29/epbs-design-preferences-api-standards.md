# ePBS Payment Config and Bid Boost Mechanisms

**Channel:** epbs | **Date:** 2025-12-29

## Summary

blockkshane is implementing the `produce_block_v4` beacon API and seeking clarification on bid boost mechanisms in the context of ePBS (enshrined Proposer-Builder Separation). They reference a payment config proposal that introduces per-builder `bid_boost` multipliers and want to understand how this relates to the existing `builder_boost_factor` mechanism.

The key question is whether the current `builder_boost_factor` will be replaced by the new per-builder `bid_boost` system, or if it will function as a global multiplier applied on top of the per-builder boosts. Additionally, they're asking whether this global multiplier would also apply when comparing peer-to-peer builder bids against local `GetPayloadResponse.block_value`.

This appears to be an open technical question with no responses yet, indicating that clarification is needed on the final specification for how these boost mechanisms will interact in the ePBS implementation.

## Participants

- blockkshane

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-29T22:53:11.791000+00:00] blockkshane: Working on `produce_block_v4` beacon API. The payment config proposal (https://hackmd.io/@tchain/payment-config) introduces per builder `bid_boost` multipliers, and  I'm assuming this is the latest spec on the topic of how boosts will be signaled from VC to BN

Today `builder_boost_factor` is the only boost mechanism as far as I'm aware. With ePBS, is `builder_boost_factor` being replaced by per builder `bid_boost`?

If not, does it become a global multiplier applied on top of per builder boosts? Would it also apply when comparing p2p builder bids against local  `GetPayloadResponse.block_value`?
```

</details>
