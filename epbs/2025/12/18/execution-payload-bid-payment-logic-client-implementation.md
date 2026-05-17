# ExecutionPayloadBid Payment Logic and Client Implementation

**Channel:** epbs | **Date:** 2025-12-18

## Summary

The discussion centers on how `ExecutionPayloadBid.execution_payment` should be implemented in consensus layer (CL) clients for bid selection logic. The key constraint is that `execution_payment` must be zero for P2P propagation to prevent DoS attacks, meaning only trustless payments can be broadcast over the network while trusted payments require direct connections to whitelisted builders.

The participants reached consensus that CL clients should implement comprehensive bid selection options, allowing proposers to consider `execution_payment` values from whitelisted builders alongside other factors like minimum bid thresholds and boost factors. This approach would enable most node operators to configure their preferred bid selection strategies directly through client settings rather than requiring external tools.

Potuz emphasized the importance of avoiding sidecars like mev-boost by making the builder API sufficiently expressive during the current standardization phase. The goal is to prevent the same implementation fragmentation issues that occurred with MEV-Boost by ensuring client implementations provide adequate flexibility for various bid selection strategies from the start.

## Participants

- austonst
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T01:13:54.281000+00:00] austonst: Are there any current expectations for the way `ExecutionPayloadBid.execution_payment` will be utilized beyond the "`execution_payment` must be zero in order to propagate over p2p" rule? I see the above discussions  not much else in search results or otherwise set in stone.

Would we expect CL clients to implement sufficient options for performing `execution_payment` vs `value` comparisons, with various min-bid-esque thresholds and boost factors and (situational) ignoring of p2p bids? I've been under the impression that CL clients would be hesitant to encode acceptance of trusted bids over trustless, but maybe it's a fine place for that logic to live.

Because the other option is a mev-boost-like sidecar listed as the single builder endpoint, multiplexing to a number of sources providing both trusted and trustless payments, and letting the sidecar implement whatever fancy math it wants for bid selection. Eventually it would pass a single bid to the CL client, whose role in bid selection would be reduced.
[2025-12-18T01:16:16.167000+00:00] potuz: over P2P we cannot broadcast a promise of payment, that's a DOS
[2025-12-18T01:16:31.387000+00:00] potuz: trusted payment has to be zero for p2p propagation, and only the trustless one is sent
[2025-12-18T01:18:17.899000+00:00] austonst: Yeah, that's fair, I wouldn't expect trusted payments to go over p2p. I was wondering more about where the logic for using `execution_payment` as an option for bid selection will live, since there will be proposers who want to consider it as an option.
[2025-12-18T01:18:35.367000+00:00] austonst: When using direct connections
[2025-12-18T01:20:00.393000+00:00] potuz: Bid selection is open for clients to decide, I assume that clients will provide tons of options, and some will allow the proposer to consider the `execution_payment` value from whitelisted builders and selet them if they are higher than other bids.
[2025-12-18T01:22:38.759000+00:00] austonst: Okay, that matches where my reasoning was heading too. As long as clients offer sufficiently expressive options, that should be enough for most node operators. A sidecar with custom logic would still be a hypothetical option if client logic were lacking, but it should be a pretty rare case.
[2025-12-18T01:26:03.981000+00:00] potuz: We want to avoid any type of sidecars, so if there's any reasonable option that needs to be expressed, it would be nice to add it to the builder API so that we avoid forking clients and/or sidecars.
[2025-12-18T01:26:29.530000+00:00] potuz: Now is a good time to standardize these flags/options and get them to be expressive and avoid the same mistakes we did with Mev Boost
```

</details>
