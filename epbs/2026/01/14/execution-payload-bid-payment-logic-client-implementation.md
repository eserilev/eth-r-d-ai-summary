# Bid Scoring and Payment Mechanisms

**Channel:** epbs | **Date:** 2026-01-14

## Summary

The discussion centered on bid scoring mechanisms and payment types in Ethereum's proposer-builder separation (PBS) system. l03443 inquired about standardization of bid scoring across clients and support for mixed trusted/trustless payments within single bids. 

potuz confirmed that the current specification supports mixing payment types additively (trusted amount + trustless amount = total payment to proposer), suggesting this approach is useful when proposers set maximum limits on trusted bid amounts they'll accept. He emphasized that while standard flags would be beneficial, the scoring mechanism should remain highly configurable by operators.

bharath.vedartham noted that block scoring mechanisms could be defined in the builder-specs (referencing existing Deneb specifications), but distinguished that such scoring should only apply to builder-API bids since peer-to-peer bids would exclusively use trustless payments. The discussion suggests a need for standardized approaches while maintaining operator flexibility in bid evaluation.

## Participants

- bharath.vedartham
- l03443
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-14T15:16:29.030000+00:00] l03443: Out of curiosity has there been any discussion on the bid scoring? would it be standardized across clients? Was wondering if clients will support a mix of trusted and trustless payments in the same bid
[2026-01-14T19:09:10.622000+00:00] potuz: It would be good to have standard flags but I think it should be highly operator configurable. The spec supports a mix so clients should support it. Notice that it's only additive so if you include x for trustless and y for trusted, the proposer will get x+y. I think such system is useful when the proposer specifies a maximum trusted bid that he's willing to accept
[2026-01-14T19:13:49.175000+00:00] bharath.vedartham: we can define a block scoring mechanism in the builder-specs like this: https://github.com/ethereum/builder-specs/blob/main/specs/deneb/builder.md#block-scoring-1 
the block scoring should only be applicable for builder-api bids i believe since the p2p bids will all be trustless payments
```

</details>
