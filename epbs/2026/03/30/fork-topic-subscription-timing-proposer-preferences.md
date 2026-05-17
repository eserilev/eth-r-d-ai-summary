# Proposer Preferences Broadcasting Implementation Discussion

**Channel:** epbs | **Date:** 2026-03-30

## Summary

The discussion centers on the implementation approach for broadcasting proposer preferences in PR #5035, specifically whether beacon nodes should broadcast preferences for both current and next epochs simultaneously or implement tracking to avoid duplicates. nflaig suggests that broadcasting both epochs each time would be simpler than implementing additional checks to track what has already been broadcast, since the gossip layer would handle deduplication through the rule that only the first valid message is processed and rebroadcast.

bharath.vedartham initially raises concerns about potential duplicates when transitioning between epochs, but agrees that letting the gossip layer handle deduplication could work. The discussion clarifies that the gossip layer's `seen_ttl` parameter maintains a 2-epoch cache window, which would effectively prevent duplicate broadcasts. Both participants conclude that this approach should be acceptable since clients would discard duplicates according to P2P rules, with bharath.vedartham noting they are "not very opinionated about it," suggesting flexibility in the final implementation choice.

## Participants

- bharath.vedartham
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-30T14:41:28.007000+00:00] nflaig: <@1386598056320831509> RE https://github.com/ethereum/consensus-specs/pull/5035, I am no sure if it's even just simpler do have the node broadcast both current and next epoch, avoids having to implement an extra check in the beacon node to track what it already has broadcast. It would be ignored and not rebroadcast by other nodes due to `[IGNORE] The signed_proposer_preferences is the first valid message`, so I would assume this is fine too
[2026-03-30T14:47:37.697000+00:00] bharath.vedartham: I think you would have to check if you broadcasted it anyways right?
Let's say we are at epoch E, we have broadcasted preferences for epoch E and epoch E+1.
Now when we arrive at epoch E+1, we already have broadcasted the preferences for epoch E+1 in epoch E so wouldn't that be duplicate then?
[2026-03-30T14:50:36.572000+00:00] nflaig: I would leave it up to the implementation, since we wanna use proposer preferences to also populate local caches might be simpler to send both epochs to beacon node each time and let gossip layer handle dedupe
[2026-03-30T15:06:34.911000+00:00] bharath.vedartham: cool but i dont think the gossip layers message cache window is an epoch long, so i assume there might be dedupe in the gossip layer too? Also i think clients would discard duplicates according to the p2p rules so i think it should be fine. I am not very opinianated about it
[2026-03-30T15:12:32.563000+00:00] nflaig: you mean `seen_ttl`? should be 2 epochs
[2026-03-30T15:15:09.967000+00:00] bharath.vedartham: yes you are right, thanks for clearing that up 👍
```

</details>
