# Builder envelope construction and beacon block root API

**Channel:** epbs | **Date:** 2026-03-12

## Summary

bharath.vedartham proposed that builders should only construct envelopes after proposers have broadcasted a beacon block that includes the bid. This approach ensures proper sequencing in the block building process.

As an action item, bharath.vedartham will update the API to pass only the beacon block root instead of the full block, determining this simplified approach should be sufficient for the current implementation needs.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-12T06:27:13.572000+00:00] bharath.vedartham: I think it makes sense for the builder to only construct the envelope only if the proposer has broadcasted a beacon block with the bid included
[2026-03-12T06:28:00.769000+00:00] bharath.vedartham: I ll update the API to just pass in the beacon block root instead of the full block. I feel like that should suffice for now
```

</details>
