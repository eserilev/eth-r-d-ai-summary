# Gossip for Bids Implementation Concerns

**Channel:** epbs | **Date:** 2026-01-22

## Summary

The discussion centers on concerns about implementing gossip for bids in the current development phase. terencechain expresses a preference to avoid implementing gossip for bids if bid construction will only be done locally, but raises a concern about potential downscoring from other clients that do implement gossip for bids. This suggests there may be interoperability or peer reputation issues if different clients take different approaches to bid gossip implementation.

The Lighthouse team, represented by 0xunclebill, echoes this sentiment and would prefer to avoid gossip for bids in devnet 0 if possible. However, no definitive decision or consensus is reached in this brief exchange, leaving the implementation approach as an open question that requires monitoring of how other client teams proceed.

## Participants

- 0xunclebill
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-22T01:23:05.770000+00:00] terencechain: If it's only local construction, I want to avoid implementing gossip for bids. But I worry if the ones that implement gossip for bids will downscore us. Something to keep an eye out
[2026-01-22T01:35:39.282000+00:00] 0xunclebill: lighthouse would love to avoid gossip for bids in devnet 0 if possible
```

</details>
