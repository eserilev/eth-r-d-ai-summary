# Gossip block handling with unknown parent hash

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers on how to handle gossip blocks with unknown parent hashes. When receiving such blocks, the consensus is that they cannot be rejected outright - instead, they must return "ignore" status in gossip handling and can optionally be queued for later processing. The recommended approach involves triggering reqresp calls to retrieve the missing payload envelope, and if that envelope contains an unknown beacon block root, continuing with additional reqresp calls to fetch the beacon block.

The process involves recursive retrieval until either reaching a block that violates finalization rules or finding a block/payload whose parent has already been imported. However, potuz notes that handling payload envelopes presents additional challenges as it represents "a dossable topic," indicating potential DoS attack vectors that need consideration in the implementation.

## Participants

- 0xunclebill
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T23:01:21.750000+00:00] nc1234: Question: if you receive a gossip block that contains bid that has an unknown `parent_block_hash`, do you reject that block or do you queue it and trigger reqresp to retrieve that payload envelope? Then, if you reqresp to get the payload envelope, and that payload envelope contains unknown `beacon_block_root`, you would reqresp to get the beacon block right?
[2026-03-05T23:32:35.975000+00:00] 0xunclebill: yeah I believe you’d keep recursing until you either reach a block that violates finalization or you reach a block/payload whose parent you’ve already imported
[2026-03-05T23:44:03.324000+00:00] potuz: You cannot reject it. You must return ignore in gossip and you can queue if you want.

We queue those. Payload envelopes it's a bit harder cause it's a dossable topic
```

</details>
