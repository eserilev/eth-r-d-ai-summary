# P2P Bid Forwarding and Rate Limiting

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion centers on rate limiting mechanisms for P2P bid forwarding in Ethereum's protocol. m.kalinin raises concerns that a 1 ETH minimum bid requirement could be circumvented by large entities creating hundreds of small builders, allowing them to send many bids per slot while still overwhelming the network with P2P messages. The fundamental question is whether the protocol expects to handle large entities creating numerous builders.

jtraglia proposes that only the highest bid should be forwarded, and suggests requiring a percentage increase (like 5%) over the previous highest bid before forwarding additional bids. m.kalinin agrees this percentage-based approach could work effectively, calculating that with a 1 gwei minimum and 3% increase requirement, 1000 bids would cost 6.8k ETH, making spam prohibitively expensive. However, he notes that malicious actors could potentially bypass this by sending different bids to different network segments, though this workaround would have limited effectiveness.

The discussion concludes with consensus on the percentage-based rate limiting approach. jtraglia commits to creating a PR later that day to add this mechanism as a single sentence in the P2P specifications for gloas.

## Participants

- jtraglia
- m.kalinin

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T12:23:39.548000+00:00] m.kalinin: I guess 1 ETH is more than enough to bid in the most of the cases. Then for large entities registering a hundred of small builders is more optimal since it will at least allow to send up to a hundred of bids each slot. Even if the withdrawability delay for builders is very high, how will that help handling thousands of P2P messages? Or we don't expect large entities to create many builders in protocol?
[2025-12-15T12:31:27.575000+00:00] jtraglia: Only the highest bid is forwarded. If that’s not good enough, we could also require a certain percentage/amount increase over the previous highest seen bid before forwarding, say 5%.
[2025-12-15T12:37:49.025000+00:00] m.kalinin: I think the highest bid won't be enough, the percentage can work. with 1 gwei as min and 3%, 1000 bids will end up with 6.8k ETH which is prohibitive enough. Also, some senders can send different bids to different parts of the network to bypass that %% check, though, this should work up to a limited extent
[2025-12-15T12:43:14.042000+00:00] jtraglia: Yup that sounds reasonable to me. I can make a PR for this later today. Should be a single sentence somewhere in the p2p specs for gloas.
```

</details>
