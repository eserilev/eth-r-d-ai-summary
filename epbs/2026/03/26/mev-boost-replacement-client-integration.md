# MEV-Boost Replacement and Client Integration Architecture

**Channel:** epbs | **Date:** 2026-03-26

## Summary

bharath.vedartham initiated a discussion about replacing MEV-Boost in the context of ePBS (Enshrined Proposer-Builder Separation), suggesting that consensus layer clients could directly implement builder bid fetching and selection functionality since ePBS eliminates blind signing. This would make MEV-Boost optional rather than a hard requirement for accessing the builder market, with the builder-specs defining client inputs like min-bid and bid-boost parameters per builder.

jordancoinbase_10730 provided detailed architectural analysis, comparing current MEV block production (builders → relays → MEV-Boost → CL) with future ePBS public P2P auctions and private auction possibilities. While agreeing that direct CL-to-builder communication is theoretically possible, Jordan advocated for an "Aggregator/Private Auction Preferences Sidecar" architecture that would act as an intermediary between builders and the CL's Builder API.

Jordan argued this sidecar approach offers significant advantages: minimal load on CL clients, separation of protocol concerns from auction aggregation, and client choice independence from auction preferences. However, they identified a key challenge in how such a sidecar would incorporate public bids without builders also pushing to it or maintaining P2P connections, suggesting the need for mechanisms to compare private auction results with public auction options based on validator preferences.

## Participants

- bharath.vedartham
- jordancoinbase_10730

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-26T16:37:59.849000+00:00] bharath.vedartham: as we are starting to discuss the builder-api, one more aspect we need to discuss is mev-boost. epbs completely removes blind signing and I think clients can implement the functionality of fetching bids across multiple builders, select the best out of them and return the beacon block with the bid to the builder. 
Most of this is likely implementation detail but in the builder-specs we should define the inputs that clients take per builder like min-bid, bid-boost etc
[2026-03-26T16:38:26.648000+00:00] bharath.vedartham: essentially we can make mev-boost optional and not have it as a hard requirement to access the builder market like today
[2026-03-26T20:22:38.238000+00:00] jordancoinbase_10730: Hmmm I think I need more clarity on what is meant here. The current landscape for MEV block production, as I understand it such (ignoring searchers as it doesn't affect this conversation):
```
Builder 1 -  
            >- Relay A
Builder 2 -            >- [MEV Boost -> CL Builder API](Proposer)
            >- Relay B
Builder 3 -
```

With ePBS public option P2P it will look like
```
Builder 1 -  
            \
Builder 2 --- P2P --- Proposers (CL)
            /
Builder 3 -
```

While I agree this is theoretically possible for a private auction
```
Builder 1 -  
            \
Builder 2 --- Proposer (CL via Builder API)
            /
Builder 3 -
```

I think this is generally much better architecture
```
Builder 1 -  
            \
Builder 2 --- [Aggregator/Private Auction Preferences Sidecar -> CL Builder API](Proposer)
            /
Builder 3 -
```

This could be some next version of MEV Boost, there could still be private relays, but the upsides:
 1) It keeps the load on CL clients minimal, the builder API can be as minimal as possible. It is not exposed directly to third parties. Less work for CL teams to maintain
 2) CL client is concerned with protocol, not extra protocol auction aggregation, preferences etc. 
3) Decisions on client to run not dictated by which supports your personal auction preferences.

The downside I see possibly here, is that there is no  mechanism for said sidecar to include public bids unless they are pushed by builders as well, or also p2p connected to listen. You could have options similar to those that exist today to either only use builder api or have preference to self built blocks that are adjusted a bit. Always use builder api blocks, or prefer bid payment vs execution payment with X percent overage needed. Then your consensus client just uses the best via private auction and best via public auction and compares with your preferences, but the private auction and all it's preferences are set elsewhere.
```

</details>
