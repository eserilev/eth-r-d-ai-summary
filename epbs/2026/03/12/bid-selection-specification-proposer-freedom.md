# Bid selection specification and proposer freedom discussion

**Channel:** epbs | **Date:** 2026-03-12

## Summary

The discussion centers on whether the Ethereum specification should define how proposers select winning bids from multiple received execution payload bids, or leave this as an implementation choice. The current spec only states that proposers should "select one bid" without specifying criteria, while the p2p interface mentions forwarding only the "highest value bid" for each slot and parent block hash. This creates potential ambiguity about whether proposers must always choose the highest-value bid or have freedom to use other selection criteria.

Several technical considerations emerged: the p2p filtering should consider both `parent_block_root` and `parent_block_hash` as a tuple to handle fork scenarios properly, and the current p2p behavior of forwarding only the highest bid may implicitly force proposers to select based on value alone. There's tension between allowing proposer flexibility (for selecting between locally built blocks, p2p bids, and builder API bids) and the expectation that economic incentives should drive selection toward highest-value bids.

The group appears to lean toward keeping the specification flexible rather than mandating specific selection criteria, with suggestions ranging from allowing "top 2 bids" to be forwarded to implementing proposer preference mechanisms that would let validators signal their priorities to the network. No firm consensus was reached, leaving open questions about balancing proposer autonomy with economic optimization expectations.

## Participants

- bharath.vedartham
- jih2nn
- jtraglia
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-12T10:09:15.132000+00:00] bharath.vedartham: The spec doesn't mention how a proposer should select a bid from a list of received bids. i.e if i receive 10 bids via the gossip layer, how is the winning bid selected. I think ideally it would be the highest bid amongst them. 
Should this be mentioned in the spec or is it left out to give the proposer freedom to choose the bid however they desire?
[2026-03-12T10:09:50.042000+00:00] bharath.vedartham: I believe all the spec says is:
```
Select one bid and set block.body.signed_execution_payload_bid = signed_execution_payload_bid." 
```
[2026-03-12T10:11:55.194000+00:00] stefanbratanov: i like this way and then clients can do whatever they prefer
[2026-03-12T10:35:00.575000+00:00] bharath.vedartham: In the p2p interface for `execution_payload_bid` , we have:
```
- _[IGNORE]_ this bid is the highest value bid seen for the corresponding slot
  and the given parent block hash.
```
1. I think this should also consider the parent block root too and not just the parent hash. 
2. I think this probably indirectly suggests that the proposer will always pick the highest value bid because only the highest value bid will be forwarded to it
[2026-03-12T11:10:40.715000+00:00] potuz: I think this should not be specified
[2026-03-12T14:14:50.407000+00:00] bharath.vedartham: Following this, i don't think we would want to spec how to handle selecting b/w locally built bids, p2p bids and bids from the builder-api and let clients do what they prefer?
[2026-03-12T17:00:37.363000+00:00] jih2nn: After discussing with <@592004585506340885>, considering a tuple of `(parent_block_root, parent_block_hash)` seems right here. Considering only one of them could be undesirable when there are forks.

Regarding 2, I personally feel like I agree with you, especially when taking the existential purpose of trustless payment into account, but couldn't come up with a good solution on the spot.
[2026-03-12T17:02:32.475000+00:00] jih2nn: Maybe it can be loosened up to, let's say, top 2 bids, but I don't have any strong opinion.
[2026-03-12T17:06:11.051000+00:00] jtraglia: This isn't my idea & I forget who mentioned it, but I would support a PR which added something to the proposer preferences for this. Which allows proposers to tell the network what is most important to them. This would only work if we can make a list of options & easily check them though.
```

</details>
