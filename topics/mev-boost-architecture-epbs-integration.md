# MEV-Boost Architecture and ePBS Integration

---

## 2026-03-26 (epbs)

Bharath initiated a discussion about how MEV-Boost would integrate with ePBS (enshrined Proposer-Builder Separation), suggesting that ePBS eliminates blind signing and allows consensus layer clients to directly fetch bids from multiple builders, select the best one, and return the beacon block. He proposed that builder-specs should define per-builder inputs like min-bid and bid-boost, potentially making MEV-Boost optional rather than a hard requirement for accessing the builder market.

Jordan provided detailed architectural analysis, comparing current MEV block production (builders → relays → MEV-Boost → CL) with future ePBS scenarios. While acknowledging that direct builder-to-proposer connections are theoretically possible, he advocated for maintaining an aggregator/sidecar approach similar to an evolved MEV-Boost. His preferred architecture would keep the CL builder API minimal and unexposed to third parties, allowing the consensus layer to focus on protocol concerns rather than auction aggregation and preferences.

Jordan outlined key advantages of the sidecar approach: reduced load on CL clients, separation of protocol and auction concerns, and flexibility in client choice regardless of auction preferences. However, he identified a potential downside where the sidecar might lack mechanisms to include public bids unless builders also push them or maintain P2P connections. The discussion remains open on how to best balance direct integration versus maintaining abstraction layers.

**Participants:** bharath.vedartham, jordancoinbase_10730

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-09 (epbs)

A single question was raised by .pinehearst asking for conceptual clarification about MEV-Boost availability following the "Glamsterdam" upgrade. They sought confirmation that MEV-Boost would not be available after this upgrade occurs.

No responses or answers were provided to this question in the discussion thread, leaving the inquiry about MEV-Boost's post-Glamsterdam status unresolved.

**Participants:** .pinehearst

<details>
<summary>Raw messages</summary>

```
[2026-04-09T17:50:05.687000+00:00] .pinehearst: I've a quick question - conceptually, mev-boost won't be available post glamsterdam, am I right?
```

</details>

