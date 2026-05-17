# Comprehensive ePBS design preferences and API standards

**Channel:** epbs | **Date:** 2025-12-14

## Summary

Potuz shared comprehensive design preferences for ePBS (enshrined Proposer-Builder Separation) ahead of a Friday call he may miss due to travel. He supports moving builders to non-validating entities using option 2 without pubkeys, but has concerns about the proposed 1 ETH minimum stake requirement as it could weaken per-builder blacklisting mechanisms by allowing builders to spin up multiple keys and rotate endpoints.

For the builder API design, he proposes a straightforward two-method approach: `get_bid` (sending parent root, parent hash, slot, proposer index, gas limit, fee recipient, and EL payment consideration) and `post_block`. He advocates for standardizing bid selection flags including `consider_trusted_bid`, `min_bid`, `min_diff`, and `blacklist_censors`, and supports signing messages salted by builder and slot to prevent bid theft between builders. Regarding implementation, he believes CL clients should implement the client side but not be mandated to implement the server side, allowing middleware solutions.

Potuz takes a firm stance against non-staked builder APIs, stating he "strongly disagrees" and will "categorically not implement any form of blind signing." He's neutral on gossiping fee recipients, viewing it as potentially overkill since proposers can verify against P2P bids, though he doesn't oppose creating the new topic given its low volume of 32 messages per epoch.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-14T11:53:28.620000+00:00] potuz: I just realized that Friday I' ll be traveling and it's not clear if I'll be on my way to the airport or be able to make the call. So wanted to leave my preferences on the many topics supposed to be discussed there:

- I support moving builders to non-validating entitites. I prefer option 2 and not use pubkeys anywhere in these helpers. I am not that certain about the 1ETH minimum for a couple of reasons, the main one is that it weakens per-builder blacklisting. I am counting on client defaults to blacklist builders that don' t deliver payloads, builders that censor etc. If a builder can spin up 32 keys instead of 1 and rotate them on their endpoint, these mechanisms become less powerful. I do not feel so strongly about this because anyway we could implement this blacklisting per endpoint instead of per builder address, but still my preference would be to revisit this minimum stake.
- On the builder API discussions. The design should be pretty straightforward: two methods: `get_bid`  and `post_block`. The second is obvious, the first one should send parent root, parent hash, slot, proposer index , gas limit, fee recipient and whether the proposer will consider EL payments or not. There is a discussion going on about if the proposer should include or not other preferences like the min bid values and such. I would not like to have these values in unless there is some clear input from proposers that they prefer to send them (eg Lido, RP, Ethstaker, Stakers union etc expressing an opinion here). 
- On the standards for bid selection. I think that's overall good if we can standardize flags for usage after ePBS. I believe we should at least have the following flags being standard: consider_trusted_bid  (bool, could be absorved in min_bid), min_bid (gwei, minimum bid that would be cosnidered for a trusted payment), min_diff (gwei, minimum bid above the trustless payment  that would be considered for a trusted payment), blacklist_censors (bool) ....
[2025-12-14T11:59:58.307000+00:00] potuz: The latest flag could be tweaked by the number of censoring payloads  that the builder sent and is dependent on EL adoption of the system. 
- On the issue of gossiping fee recipient and similar. I don' t mind it, but I think it's overkill. These lists can be kept off-protocol, proposers can' t be tricked cause they verify their fee recipient against the p2p bid. Anyway, I do not oppose the creation of the new topic since in principle is very low volume having only 32 messages per epoch.
- On the issue of who implements the builder API. I think CL clients need to implement the client side of it, but we should not be mandated to implement the server side of it. I think it's fine to rely on middleware to be the server side of the builder API and anyway most probably this will exist. People from the PBS foundation are alerady working on versions of this and also in standardizing listing of builder' s endpoints. 
- I forgot one thing on the builder API of course: I support signing messages salted by the builder so that builders can protect from other builders trying to get their bids. This should be salted by builder and slot.
[2025-12-14T12:01:07.828000+00:00] potuz: Finally if the issue of non-staked builder API comes up, I strongly disagree and we will categorically not implement any form of blind signing.
```

</details>
