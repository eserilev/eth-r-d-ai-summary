# Consensus specs PR 5181 and beacon_blocks_by_head endpoint discussion

**Channel:** consensus-dev | **Date:** 2026-05-14

## Summary

The discussion centers around PR 5181 which introduces a `beacon_blocks_by_head` endpoint for Ethereum consensus clients. The main debate involves whether the current implementation unnecessarily serializes downloads when parallel processing could be more efficient. Arnetheduck argues that requesting blocks by root hash with a count parameter enables automatic slot-gap skipping (since returned blocks contain parent hashes for subsequent requests) while allowing parallel downloads from multiple peers, similar to BitTorrent's endgame strategy for handling slow peers.

The conversation reveals competing approaches to sync optimization. Dapplion advocates for the current design that requests by count rather than slot range, explaining it eliminates ambiguity between honest long skips and malicious block omission that complicates existing `beacon_blocks_by_range` implementations. However, Arnetheduck contends this approach is inherently slow and proposes that requesting ranges by root naturally detects malicious peers (since honest peers must return at least the root-specified block) while enabling better parallelization. Kasey suggests an alternative approach using root-only responses for cheap divergence point detection, though this adds round-trips.

Key concerns include DoS protection (Arnetheduck suggests disk storage bounded by finality points), the timing pressure since clients will begin implementation soon, and whether to delay the endpoint for further refinement. The discussion also touches on adding blinded block variants for pre-Gloas efficiency. No clear consensus emerges, with participants noting the need for broader client team input before the upcoming release.

## Participants

- __kasey__
- arnetheduck
- dapplion
- jtraglia
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-14T16:24:58.868000+00:00] arnetheduck: https://github.com/ethereum/consensus-specs/pull/5181#discussion_r3239617125 - I like the idea (hence the thumbs up) but there are improvements to be had to enable parallel downloads, and perhaps worth discussing
[2026-05-14T16:27:53.759000+00:00] jtraglia: But to <@688748669268132001>'s comment, the point is not to think about slots here.
[2026-05-14T16:29:08.430000+00:00] jtraglia: Also, given that the response is limited to 128 (right?) is it even worth doing it in parallel?
[2026-05-14T16:33:14.953000+00:00] arnetheduck: it's unnecessary - if you have a block hash and send a req for `(hash, 0, 32)` it means you get at least one block from that request - in the rare case that there is a big gap, you automatically get the parent hash (which is in the one block you're guaranteed to get) so the _next_ request will skip over any number of slots - but in the common case of small gaps, you get to download non-overlapping ranges.

it is _always_ worth doing in parallel - ie if you hit a slow peer, you don't want to wait for that slow peer - instead, you want to keep downloading blocks til you reach your "known anchor" (or past finality) to determine whether the branch is useful or not - in the "end game" you also request the _same_ range from multiple peers (similar to how bittorrent clients first download random chunks from random peers then start requesting the _same_ chunk from mulitple peers so you don't end up waiting for that last chunk for ever)
[2026-05-14T16:36:42.244000+00:00] jtraglia: Hmm okay. It would have been good to chat about this at acdc. If you want to make changes to the endpoint, we should act quickly because clients are going to start implementing this soon.
[2026-05-14T16:37:25.406000+00:00] jtraglia: Do you want to suggest we remove this endpoint so it's not included in the upcoming release?
[2026-05-14T16:38:23.413000+00:00] jtraglia: You'll need to get other clients to weigh-in here. cc <@833706406699073536> <@755590043632140352> <@785811170387951616>
[2026-05-14T16:40:08.922000+00:00] arnetheduck: I mean .. it does help one particular way of syncing, but imo that way of syncing is inherently slow and unnecessarily so ... I think the _idea_ of anchoring a range request in a particular branch/root is sound but as it stands, we're leaving value on the table by crippling the request with unnecessary serialization .. I would _lean_ towards giving it more time, also because it's trivial to implement but much harder to remove (clients just have to copy-paste their current range request and change the filter condition a little bit)
[2026-05-14T16:46:46.276000+00:00] jtraglia: Also <@586161934425128960> if you're still working. Ignore if you're not.
[2026-05-14T16:47:51.165000+00:00] nflaig: <@534934855113506836> was working on that from our side
[2026-05-14T17:14:24.913000+00:00] __kasey__: I talked to Lion about this way back in Berlin - my first preference was an endpoint that either gives a list of roots leading up to the requested root, or if we want the ability to do more verification, headers. Then as you say we could request individual blocks by root from any peer on that branch.

I also plan to submit a PR to propose all block endpoints to have a blinded variant. Particularly in the case of backfill, it's a complete waste to marshal and send the execution payload, and if the sender stores blinded blocks (which prysm does by default), populating the payload in the block requires an execution engine request.
[2026-05-14T17:15:36.160000+00:00] arnetheduck: https://github.com/ethereum/consensus-specs/pull/5181#discussion_r3243044923 - we can have the slot skipping propery _and_ parallel ranges
[2026-05-14T17:17:12.040000+00:00] arnetheduck: we discussed header downloads in norway and concluded that the advantages are very limited - in the _normal_ case the range of blocks you're looking for is good so issuing header requests _then_ body requests just slows you down unnecessarily (ie it makes the normal case slower) - the only thing that strategy protects against is someone giving you junk which is  quite unusual.
[2026-05-14T17:18:39.145000+00:00] arnetheduck: gloas blocks are already blinded?
[2026-05-14T17:18:39.777000+00:00] __kasey__: Yeah I prefer the list of roots response. It's a cheap index read to generate the response, it's tiny/fast to download. You can make that request to multiple peers and then download the list of roots that has the most agreement.
[2026-05-14T17:18:56.766000+00:00] __kasey__: Yeah obviously a moot point for gloas, only meant for pre-gloas.
[2026-05-14T17:21:20.704000+00:00] arnetheduck: it's still extra round-trips - and downloading just roots is not verifiable - you can't trust someone that tells you that the branch is "not interesting", ie you still have to download blocks (or headers) from that branch no matter what they claim
[2026-05-14T17:26:30.417000+00:00] __kasey__: I don't think of it as extra round trips because I envisioned using the requests at different points in time. If I see peers with a different head, I can request the list of roots, identify where their history diverges from mine, and asynchronously download the intervening blocks if it seems interesting (ie I see the same branch in multiple peers). I can incrementally verify that branch by starting my download at the diverging block and performing gossip verification on it.
[2026-05-14T17:32:04+00:00] arnetheduck: fair, finding divergence point cound indeed save a few downloaded blocks of overshoot on the range
[2026-05-14T18:04:44.484000+00:00] dapplion: To recap: the massive simplification in sync comes from building a block tree in memory from all peer's heads rooted in something we know. Then forward syncing. Answering to <@833706406699073536> y proposal, fetching roots only doesn't help because we can't verify the chain of blocks. Headers is the most efficient, but <@449019668296892420> rejected it due to the need of an additional DB index. Then here is where `beacon_blocks_by_head` comes in.
[2026-05-14T18:05:11.015000+00:00] dapplion: If `beacon_blocks_by_head` request as slot range, and the peer returns empty, we have two possible scenarios:
- There was a long skip (honest)
- Peer omiting blocks (malicious / faulty)

We have no way of telling those two cases apart. `beacon_blocks_by_range` suffers the same issue and this complicates sync significantly in Lighthouse codebase. The point of `beacon_headers_by_head` requesting a `count` is to eliminate that edge case.
[2026-05-14T18:05:24.614000+00:00] dapplion: <@449019668296892420> if you want to request sequential slot ranges you need to keep then in memory as you can't validate the range `N` until you validate range `N-1`. How do you handle the DoS OOM risk in that case? How many ranges will you keep in memory?
[2026-05-14T18:11:24.416000+00:00] arnetheduck: two ways:
* store the blocks on disk, in a temporary location - it's bounded by your finality point so there's really not much damage that _can_ be done, ie your client _must_ be able to store this much data anyway
* drop them and redownload if valid, with bounded lru - combine with above for simple / small cases (as is typical)

it's not really a profitable deliberate DoS because the peer has to upload as much data as you have to download and the data has to be well-formed, so a malicious peer has to work to generate it - that leaves buggy peers, which indeed might force you to waste some bandwidth finding out that a history  is useless .. 

_however_ - we also discussed mitigation strategies in general .. ie you order your branch downloads by the number of peers that report a particular head - they could all be lying to you of course but the point is mostly that a single peer can't make you download anything more than `head_slot - finalized_slot` blocks which your client can _always_ handle
[2026-05-14T18:12:57.869000+00:00] arnetheduck: we do: we request a range by _root_ - that means that the honest peer returns _at least_ one block (the one selected by block_root) - that block contains the parent root - the next request will skip the slot gap by construction - ie you _always_ detect the "peer omitting blocks" case that can happen with the current range request
```

</details>
