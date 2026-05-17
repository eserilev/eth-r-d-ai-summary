# Bid Size and KZG Commitments in Execution Payload

**Channel:** epbs | **Date:** 2026-01-09

## Summary

The discussion centers on concerns about including full KZG commitments in execution payload bids due to bandwidth implications. Potuz explains that bids are in the "hot path for propagation" and will become very large when the system scales, making it problematic to include full KZG commitments in the `SignedExecutionPayloadBid` structure that every bidder submits via P2P gossip and HTTP requests to trusted builders.

Marcopolo raises the issue that KZG commitments are redundant across data columns in data column sidecars and questions whether they could be moved to a smaller propagation message. However, Potuz clarifies that the current approach uses only a root to commit to the KZG commitments (for equivocation protection) rather than the full commitments, and argues that adding all full commitments would be detrimental. The conversation concludes with pawandhananjay agreeing that adding full commitments to the bid container is not ideal given its dual use for both P2P propagation and actual bidding.

## Participants

- marcopolo__
- pawandhananjay
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-09T00:01:51.230000+00:00] potuz: The original reason and I believe it's still valid is that when we scale this will make the bid very large
[2026-01-09T00:03:16.307000+00:00] potuz: Bids are in the hot path for propagation, we don't want to have them very large
[2026-01-09T00:48:45.248000+00:00] marcopolo__: Does each bidder submit a `SignedExecutionPayloadHeader` per bid? and does it submit this by broadcasting it on gossipsub? If so, I think I agree we don't want to put the kzg commitments there since there will be many bids that aren't accepted and those commitments are just wasted bandwidth.

Is there a way we can move the `kzg_commitments` out of the data column sidecar? It's redundant across data columns. Is there a small message they could propagate on?
[2026-01-09T00:57:25.951000+00:00] potuz: It submits a `SignedExecutionPayloadBid` which is I think what you have in mind. The reason there's a root is just to commit to the kzg commitments on the block in case of equivocations. It can be removed entirely (going in the opposite direction of what's being suggested here) but I think adding them all is a bad idea
[2026-01-09T01:00:55.951000+00:00] marcopolo__: sorry, I was looking at an older version of the spec. `SignedExecutionPayloadBid` is what I meant. And every bidder submits one, correct? Or is there just one per slot?
[2026-01-09T04:49:49.213000+00:00] potuz: Every bidder on P2P, but most importantly the proposer will request this over http on the hot path to trusted builders
[2026-01-09T05:01:16.860000+00:00] pawandhananjay: ahh i did't realise it was the same container we use for the actual bids too. Yeah adding the full commitments there doesn't seem ideal then
```

</details>
