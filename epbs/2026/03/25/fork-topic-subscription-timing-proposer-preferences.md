# Fork topic subscription timing and proposer preferences

**Channel:** epbs | **Date:** 2026-03-25

## Summary

The discussion centers around the timing for subscribing to fork topics and broadcasting proposer preferences for the upcoming "gloas" fork transition. A GitHub PR (#4947) highlighted inconsistencies in client implementations regarding the spec requirement that nodes should subscribe to new fork topics at least one epoch before the fork occurs.

Different clients currently use varying approaches: Lodestar subscribes two epochs ahead and broadcasts proposer preferences at the start of the last "fulu" epoch, while Lighthouse only subscribes 2 slots before the fork (which has worked for building gossip mesh since Altair). The team discussed whether broadcasting proposer preferences mid-epoch is appropriate, with some suggesting that 2 slots may be sufficient for mesh building but potentially too short for processing millions of validator messages. The conversation revealed that Lighthouse would need to adjust its approach to comply with the "at least one epoch" spec requirement, effectively subscribing 1 epoch + 2 slots before the fork.

The discussion concluded with recognition that builders need sufficient time to process preference messages, and there was suggestion that the specific timing requirements should be clearly specified rather than left to individual client interpretations. No final consensus was reached on the exact timing for broadcasting proposer preferences, leaving this as an open implementation question.

## Participants

- .paulharris
- jameshe5018
- nflaig
- pawandhananjay

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
