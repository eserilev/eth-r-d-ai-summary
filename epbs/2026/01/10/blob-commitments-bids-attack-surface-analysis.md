# Blob Commitments in Bids Attack Surface Analysis

**Channel:** epbs | **Date:** 2026-01-10

## Summary

terencechain raised concerns about the attack surface created by including blob commitments in bids within the Ethereum block building process. They questioned whether allowing nodes to call `getBlobs` on winning bids provides meaningful latency or bandwidth benefits compared to waiting for the execution payload, noting that no one has quantified these potential savings.

The main security concern centers on the size impact of full commitments in bids - approximately 6KB per bid (48 bytes × 128 blobs) - which could strain the already fragile P2P network layer. While existing defenses include one bid per builder limits and price increment rules before forwarding, terencechain highlighted these protections are easily circumvented. An attacker with around 100 ETH could create 100 unique builder indices and spam low-value bids starting at 1 gwei, potentially exploiting this attack vector.

The discussion leaves open the question of whether the performance benefits justify the increased attack surface, with no quantitative analysis available to support either position.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-10T05:20:39.368000+00:00] terencechain: I am trying to understand how much real benefit there is in letting nodes call `getBlobs` on the winning bid versus waiting for the execution payload itself. has anyone tried to quantify the latency or bandwidth savings in this model?

My concern is that including full commitments in bids can eventually become an attack surface. At the end, 48B times 128 blobs is a ~6 KB per bid.  Even if most winning bids come over RPC via pulling, P2P is still relevant and already somewhat fragile. We have defenses like one bid per builder and price increment rules before forwarding, but those seem easy to game. With something like 100 ETH, an attacker could spin up 100 unique builder indices and start bidding at 1 gwei... etc
```

</details>
