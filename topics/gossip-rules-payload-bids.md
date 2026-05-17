# Gossip Rules for Payload Bids

---

## 2026-04-14 (epbs)

Pawandhananjay raised a question about which state should be used when validating gossip rules for payload bids, specifically for the `can_builder_cover_bid` check. They noted that as a proposer, only bids with `parent_block_root` matching their canonical head would be useful, so it would make sense to check against that state. However, they expressed concern that loading states corresponding to different parents could create a DoS risk without providing significant benefits.

Potuz responded that many gossip rules suffer from similar underspecification regarding which state to use, and suggested that the intention is likely to use the head state of the validating node for all such checks. The discussion appears to highlight a broader issue with gossip rule specifications lacking clarity about state references. Nflaig began to respond about copying not being an issue for their implementation, but the message was cut off, leaving the technical solution approach incomplete.

**Participants:** nflaig, pawandhananjay, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-14T15:55:42.556000+00:00] pawandhananjay: Quick question about the gossip rules for payload bids. The spec says
> [IGNORE] bid.value is less or equal than the builder's excess balance -- i.e. can_builder_cover_bid(state, builder_index, amount) returns True.

What state are we using here? as a proposer, the only useful bids for me would be ones with `parent_block_root` as my canonical head, so would make sense to only check against that state. 
If we start loading states corresponding to the parent, then it opens us up to a dos risk for not much gain. How are other clients handling this?
[2026-04-14T15:56:35.172000+00:00] potuz: many gossip rules are underspecified in this same regard IMO, I take it that they all refer to the head state of the validating node
[2026-04-14T16:02:52.271000+00:00] nflaig: copying isn't an issue for us, but
```

</details>

