# Fork topic subscription and proposer preferences timing

---

## 2026-03-25 (epbs)

The discussion centers around the timing requirements for subscribing to fork topics and broadcasting proposer preferences during the transition from the "fulu" to "gloas" Ethereum fork. A bug was discovered related to not adhering to the specification requirement that nodes should subscribe to new fork topics at least one epoch before the fork occurs. 

Different client implementations currently use varying approaches: Lodestar subscribes two epochs ahead and broadcasts proposer preferences at the start of the last fulu epoch, while Lighthouse subscribes only 2 slots before the fork (which has worked for building gossip mesh since Altair). The specification states nodes "SHOULD subscribe to this topic at least one epoch before the fork," meaning Lighthouse would need to adjust to 1 epoch + 2 slots before to comply. The longer timeframe is necessary not just for building gossip mesh, but also to give builders sufficient time to process proposer preference messages before they're needed for block proposals.

The conversation reveals some uncertainty about optimal timing, with suggestions ranging from mid-epoch broadcasting to maintaining the minimal 2-slot window that has historically worked for mesh building. There's recognition that this should be properly specified rather than left to individual client interpretation, though no firm consensus on exact timing was reached in this discussion.

**Participants:** .paulharris, jameshe5018, nflaig, pawandhananjay

<details>
<summary>Raw messages</summary>

```
[2026-03-25T19:35:18.068000+00:00] jameshe5018: https://github.com/ethereum/consensus-specs/pull/4947 <@1386598056320831509> brought this up as we had a bug not adhearing to this, are other interpreting this as  subscribing to the gloas topic and publishing signed proposer preferences with gloas fork digest while still in fulu? it words at least 1 epoch so terence suggested we do it from 2 epochs  but what do others think?
[2026-03-25T21:34:23.673000+00:00] nflaig: lodestar already subscribes to new fork topics two epochs ahead, and then at the start of last fulu epoch we will broadcast proposer preferences for first gloas epoch
[2026-03-25T21:38:40.692000+00:00] pawandhananjay: lighthouse currently subscribes 2 slots before the fork. any specific reason why we need to subscribe 1 epoch in advance?
[2026-03-25T21:40:01.793000+00:00] jameshe5018: the proposer preferences for gloas should be broadcasted before gloas so that information can be used when gloas blocks are proposed
[2026-03-25T21:40:43.295000+00:00] .paulharris: to find a decent mesh before fork iirc
[2026-03-25T21:40:53.851000+00:00] jameshe5018: we subscribe to topics 1 epoch before a fork, and based on kasey's suggestion we'll broadcast a few slots in ( i'm picking mid epoch) to avoid a race
[2026-03-25T21:41:02.593000+00:00] nflaig: but 34 slots in advance would do it I guess? if 2 slots worked for LH so far?
[2026-03-25T21:41:45.659000+00:00] .paulharris: 2 slots is probably fine as long as your peers arent totally awful i'd think
[2026-03-25T21:42:29.608000+00:00] .paulharris: maybe a libp2p person would correct me though, im making naive assumptions here
[2026-03-25T21:43:46.562000+00:00] pawandhananjay: for building a mesh, i think 2 slots has been sufficient for us. we have been doing that since altair
[2026-03-25T21:48:24.591000+00:00] jameshe5018: is broadcasting mid epoch too early then? if lighthouse only subscribes 2 slots before
[2026-03-25T21:55:45.206000+00:00] .paulharris: it should probably be a spec item not a 'if client X does...' thing
[2026-03-25T21:56:54.211000+00:00] .paulharris: if its a message for a million plus validators, a window of 2 slots seems decidedly small, but if its just for proposers in next epoch you could see it being perfectly fine
[2026-03-25T21:57:35.086000+00:00] nflaig: I mean lighthouse has to make an exception here, otherwise it's not per spec note
> Nodes SHOULD subscribe to this topic at least one epoch before the fork
[2026-03-25T21:57:53.105000+00:00] .paulharris: oh nice if its in the spec
[2026-03-25T21:58:14.816000+00:00] nflaig: how I understood is for LH it will be 1 epoch + 2 slots before
[2026-03-25T22:00:03.964000+00:00] pawandhananjay: i'm curious if the one epoch duration is only for giving enough time to build the mesh or if there's any other reason too. If its just building the mesh, then i think 2 slots is sufficient and the spec can just say "Nodes SHOULD subscribe to this topic before the fork"
[2026-03-25T22:00:39.127000+00:00] pawandhananjay: ohh this is for the preferences, sorry ignore me
[2026-03-25T22:01:25.787000+00:00] pawandhananjay: the builders need sufficient time to process the preferences msg too, got it
[2026-03-25T22:01:51.451000+00:00] .paulharris: it'd really depend how many messages need propagating and if you're going to see them in time i guess
```

</details>

---

## 2026-03-30 (epbs)

The discussion centers around implementation strategies for proposer preferences broadcasting in PR #5035, specifically whether nodes should broadcast preferences for both current and next epochs simultaneously. nflaig suggests this approach could simplify implementation by avoiding extra tracking logic in beacon nodes, relying instead on the gossip layer's deduplication mechanism that ignores duplicate messages based on the "first valid message" rule.

bharath.vedartham raises concerns about potential duplication issues when transitioning between epochs, noting that preferences for epoch E+1 broadcasted during epoch E would become duplicates when actually reaching epoch E+1. However, both participants conclude this shouldn't be problematic since the gossip layer's message cache (`seen_ttl`) maintains a 2-epoch window for deduplication, and clients will discard duplicates according to p2p rules.

The discussion concludes with agreement that the implementation approach is flexible - nodes can broadcast both epochs and let the gossip layer handle deduplication, with bharath.vedartham noting they are "not very opinionated about it," suggesting this remains an open implementation choice for client teams.

**Participants:** bharath.vedartham, nflaig

<details>
<summary>Raw messages</summary>

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

