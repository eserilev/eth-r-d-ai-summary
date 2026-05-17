# ExecutionPayloadBid KZG Commitments and Blob Optimization

**Channel:** epbs | **Date:** 2026-01-08

## Summary

The discussion centers on a potential optimization issue in the `ExecutionPayloadBid` specification, where it currently contains only a `blob_kzg_commitments_root` instead of the full KZG commitments. This design choice prevents making `getBlobs` requests until receiving the complete payload, which could limit bandwidth optimization opportunities.

The main benefit of including full KZG commitments would be enabling partial columns optimization for `getBlobs`. With full commitments available when the consensus block arrives, builders could immediately push cells and metadata for private blobs, allowing nodes to have complete columns available before the payload arrives and reducing reliance on gossip networks for bandwidth savings. Additionally, having either full commitments or versioned hashes would allow proposers to select bids containing blobs they specifically need, and enable earlier peer communication about available and missing cells to reduce latency.

Terence noted that the current ePBS specification doesn't account for partial columns optimization and acknowledged this may be an oversight, though he initially questioned whether the timing difference between bid receipt and payload envelope receipt was significant enough to matter. The group seems to lean toward including full commitments if there's no compelling reason against it, with Kasey suggesting versioned hashes as an alternative solution.

## Participants

- __kasey__
- marcopolo__
- pawandhananjay
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-08T19:54:34.177000+00:00] pawandhananjay: Is there any reason why the `ExecutionPayloadBid` contains a `blob_kzg_commitments_root` instead of the full kzg commitments? doesn't that mean we cannot make a getBlobs request until we receive the full payload?
[2026-01-08T22:25:11.501000+00:00] terencechain: I think it may be an oversight that forgets we could use get globs optimization once you see the kzg commitments in the bid

But you could still call get blobs once you see the kzg commitments in the payload envelope itself. DA deadline will be later than execution deadline as one example 

Is there a strong reason why one would want to call get blobs on a winning bid vs receiving the payload envelope? The timing diff is small here right?

I don't have a strong opinion
[2026-01-08T22:35:05.542000+00:00] pawandhananjay: This came up in the context of the partial columns optimisation to getBlobs.  cc <@795439202732867594> <@106441459183423488> 
If we have the full kzg commitments, we can call getBlobs immediately when the consensus block is available. the builder can push the cells + metadata for the private blobs and all nodes might already have the full columns available by the time the payload arrives. Most columns won't have to take the gossip route and we can save on bandwidth there. 
If there is no good reason not to have the full commitments, i think it might be better to have it
Also cc <@833706406699073536> since he brought it up on the other call
[2026-01-08T22:38:21.753000+00:00] marcopolo__: > Is there a strong reason why one would want to call get blobs on a winning bid vs receiving the payload envelope? The timing diff is small here right?

The sooner we can call getBlobs the sooner we can share with our peers what cells we have and which are missing. Lowering latency in this exchange would be very nice.
[2026-01-08T22:44:17.309000+00:00] __kasey__: I think versioned hashes would also work? Also either commitments or versioned hashes would enable proposers to pick bids that include blobs they care about.
[2026-01-08T23:53:56.412000+00:00] terencechain: that makes sense, the current epbs spec is not aware of partial columns optimization
```

</details>
