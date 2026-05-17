# Builder URL Discovery and Distribution Mechanisms

**Channel:** epbs | **Date:** 2025-12-04

## Summary

The discussion centers on how proposers can discover and maintain URLs for staked builders in a new system. bharath.vedartham proposed using a global p2p gossip topic where staked builders could broadcast their URLs, with proposers maintaining an internal map updated via builder signatures, though URL validation would remain challenging.

stefanbratanov suggested that builder URLs would likely be widely available similar to current relay URLs, making manual configuration of consensus layer clients sufficient. bharath agreed this approach should work initially, noting that while scaling concerns exist if many validators become builders, not all would necessarily expose URLs publicly.

potuz outlined a phased approach starting with compiled lists, mentioning discussions with MEV-boost collaborators about implementing a checkpoint sync-like standard for nodes to fetch URL lists from servers. As an alternative "cypherpunk option," he suggested clients could fetch builder information from an on-chain contract, providing a more decentralized discovery mechanism.

## Participants

- bharath.vedartham
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-04T07:39:28.932000+00:00] bharath.vedartham: a thought, would it make sense to a global p2p gossip topic for staked builders to broadcast their urls to fetch the execution payload bid? i would assume the proposer can keep an internal map and update it whenever a url is broadcasted. We could validate such messages by the builder signature. Although we can't easily validate the URL.
[2025-12-04T07:53:54.692000+00:00] stefanbratanov: I am thinking those would be widely available similar to relays urls nowadays and would be just a case of configuring each CL with the urls
[2025-12-04T07:59:54.792000+00:00] bharath.vedartham: yeah true. i think it should be enough too IMO. ofc if we run into a situation where there are too many validators who become builders it can get harder to maintain a list. but i don't know if a lot of such validators will expose URLs too
[2025-12-04T10:47:37.703000+00:00] potuz: As a first start we can rely on compiled lists. We talked to some current MeV boost collaborators about implementing a standard like checkpoint sync so that the node can grab these lists from servers. 

A cypherpunk option is to have clients fetch this from a contract on chain
```

</details>
