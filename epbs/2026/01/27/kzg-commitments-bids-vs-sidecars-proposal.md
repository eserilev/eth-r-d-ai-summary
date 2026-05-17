# KZG commitments in bids vs sidecars proposal

**Channel:** epbs | **Date:** 2026-01-27

## Summary

The discussion centers on a proposal to include KZG commitments directly in execution payload bids rather than in data column sidecars, which would add approximately 3,424-3,500 bytes to each bid (48 bytes × 72 blobs). The main benefit would be allowing nodes to request blobs from the execution layer before builders even broadcast sidecars, and reducing bandwidth by removing commitments from sidecars and payloads. However, concerns were raised about the increased bid latency in the time-sensitive auction process, potential "free DA" issues where commitments could remain on-chain even if blobs aren't available, and the fundamental problem of constraining payloads before they need to be constructed.

Key technical considerations include whether to use versioned hashes (32 bytes) instead of full commitments (48 bytes) to reduce overhead, and the impact on sidecar verification which currently allows self-contained validation but would become dependent on receiving bids first. The discussion revealed uncertainty about the actual bandwidth impact on the bidding p2p stack and whether the latency increase might discourage builders from including blobs. There's also concern that this change could complicate future slot auction mechanisms.

No firm decision was reached, with participants agreeing this wouldn't be suitable for devnet 0 due to structural complexity, though it could potentially be implemented later if bandwidth and latency concerns prove manageable. The proposal was shared in other channels for broader builder feedback.

## Participants

- .paulharris
- __kasey__
- jtraglia
- potuz
- ralexstokes
- raulvk

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T17:14:44.285000+00:00] jtraglia: > It would be better if we had the full list of commitments in the bid already so that clients could request the blobs from the EL directly before even any sidecar arrives, but this would bloat the p2p network for bids, and the proposer <-> builder direct connection when requesting the bids.
Would it really be that much bloat? 48 bytes * ~72 blobs = ~3500 bytes. Not nothing, but not that much.
[2026-01-27T17:15:36.019000+00:00] jtraglia: Would this allow us to remove the commitments from the data column sidecars? Potentially saving us bandwidth?
[2026-01-27T17:17:06.272000+00:00] jtraglia: Because we'll always have the `ExecutionPayloadBid` before receiving & verifying sidecars, right?
[2026-01-27T17:19:33.633000+00:00] jtraglia: You could store the commitments root in `DataColumnSidecar` but I don't see the point in that.
[2026-01-27T17:19:38.643000+00:00] jtraglia: cc <@755590043632140352>
[2026-01-27T17:21:24.211000+00:00] potuz: I think it's a lot in the most time sensitive piece of the auction
[2026-01-27T17:27:28.772000+00:00] jtraglia: That's fair. The other benefits would be nice though.
[2026-01-27T17:31:22.492000+00:00] jtraglia: We'd be able to remove the commitments from `ExecutionPayloadEnvelope` too.
[2026-01-27T17:33:12.347000+00:00] jtraglia: With a 15Mbps connection, an extra 3500 bytes would add ~2ms of latency.
[2026-01-27T17:37:10.701000+00:00] jtraglia: Across 10 hops, ~20ms. Or something like that.
[2026-01-27T17:42:24.505000+00:00] potuz: you can add it to the agenda, but I don't know if we have a place to discuss sync anymore. Perhaps we can tag builders here to gauge?
[2026-01-27T17:44:41.768000+00:00] jtraglia: Shared in the <#692078615269212180> channel. I'll think about adding it to the agenda. Might come to a quick resolution here first though.
[2026-01-27T17:45:48.096000+00:00] potuz: Another problem which is perhaps more fundamental is that it creates free DA
[2026-01-27T17:46:16.514000+00:00] potuz: the commitments stay with the block even though the payload may never be available
[2026-01-27T17:46:45.721000+00:00] potuz: it's a cheap form of DA though cause the blobs are not with nodes, but still you get to post 3MB on chain for the WSP
[2026-01-27T17:47:33.975000+00:00] jtraglia: We could limit the number of commitments in the bid to be <= the current blob limit.
[2026-01-27T17:47:54.128000+00:00] jtraglia: Still free DA, but much less. 1 KiB right now.
[2026-01-27T17:49:15.966000+00:00] jtraglia: But yes, this would affect state growth too. Since the bid is stored in the state.
[2026-01-27T17:50:10.719000+00:00] jtraglia: I think that alone is reason enough to not make this change.
[2026-01-27T17:50:53.522000+00:00] jtraglia: So yeah nevermind. This is a bad idea 😅
[2026-01-27T18:52:24.976000+00:00] potuz: Oh lol, I said MB above 🙂
[2026-01-27T18:53:57.039000+00:00] potuz: The bid is not stored in the state though, just one bid, should not be bad. And getting the blobs fom the EL seems to be a game changer at least now. If people are not worried about bandwith or the quick free DA we could probably do it. It can be done later too
[2026-01-27T18:54:09.877000+00:00] potuz: definitely not devnet 0 though, please no more structure changes now 🙂
[2026-01-27T20:08:23.785000+00:00] potuz: <@795439202732867594> can you weigh in on the above? the suggestion is to move the full KZG commitments to the bid instead of the payload and all data column sidecars.  Pros (assuming 72 blobs)
- Nodes can request the blobs from the EL before the builder even decided to broadcast them. 
- 3476 bytes less on each sidecar and each payload

Cons: 
- 3424 bytes more on each bid: this means the bid goes from 212 bytes to 3636 bytes. Bids can be broadcast by any builder and is a global topic. 
- Larger bids means more latency on the auction itself, bids are requested directly from the builder by validators, if this latency is important, builders may choose not to include blobs as they do today. 

Perhaps the biggest con is a metacon: I think we want to move to a world in which the bid does not identify anything from the payload, really removing most synchronization and commit/reveal mechanisms from the slot.
[2026-01-27T20:17:16.285000+00:00] raulvk: let me think carefully about this. as an early data point, Xatu Sentry nodes consistently see >93% of blobs in the public mempool before they're included, with the other ~6% being received very shortly after the slot (1-10s). Overall, blobs wait a median of ~7-10s, and a p99 of <20s before inclusion.

https://observatory.ethp2p.dev/latest/mempool-visibility

caveat: Xatu Sentries are well connected machines with 1Gbps bw (possibly higher), so this picture is not representative of the whole network. but it does provide insight into pre-slot availability.
[2026-01-27T20:18:55.961000+00:00] raulvk: a couple of key points:
- as we stretch towards 72+ blobs, we'll start seeing more fragmentation of blobpool availability
- scaling blobs involves smoothening out bandwidth utilization over time (so instead of relying on critical path propagation, we rely more on the blobpool)
[2026-01-27T20:25:51.325000+00:00] raulvk: the last point is operative: the path to scaling blobs with the PeerDAS architecture leans on pre-propagation (both cell level messaging and the sparse blobpool leverage this), so from that perspective, we're fine taking a tradeoff in ePBS that reveals included blobs in a bid. on the flip side, it also gives builders more opportunity to withhold blobs from the blobpool and rely on the extra time to propagate them in the critical path. hmmm
[2026-01-27T20:27:13.440000+00:00] raulvk: on the variable PTC front, it's feels easier to reason about the function if blobs are not part of the exec payload
[2026-01-27T20:27:33.142000+00:00] potuz: blobs aren't part
[2026-01-27T20:27:39.487000+00:00] potuz: we're talking purely about the commitments here
[2026-01-27T20:28:13.718000+00:00] raulvk: i see, let me think more about this
[2026-01-27T20:32:51.349000+00:00] raulvk: <@755590043632140352> to clarify, when i say "blobs" it's a shorthand for revealing which blobs are included (i.e. the commitments). i don't mean inlining blob data, if that's what you were commenting on?
[2026-01-27T20:33:37.909000+00:00] potuz: then what does this comment mean?
> on the variable PTC front, it's feels easier to reason about the function if blobs are not part of the exec payload
[2026-01-27T21:47:36.482000+00:00] raulvk: <@755590043632140352> sorry, was running on bad info, disregard the variable PTC comment. here are my thoughts -- pls flag if something is off:
- the current spec makes DataColumnSidecar validation dependent on having received the BeaconBlockBody. in the past we could validate and forward DCS before receiving the BBB. the combination of kzg commitments, kzg cell proofs, signed block header, and inclusion proof, enabled us to authenticate columns independently; now we have to wait because we cannot legitimize the column before the block arives.
- this might be problematic because, today, the majority of columns are received [***before*** the block](https://observatory.ethp2p.dev/latest/block-column-timing#Block-to-column-delay-over-time), so this new dependency can cause a net slowdown in column propagation.
- however, my understanding is that we're betting that the reduced size of the beacon block (sans exec payload) will allow it to propagate faster, offsetting the potential delay (this is something we'll have to test carefully to see if it checks out; it's possible that block validation dominates path latency).
- in summary, keeping the KZG commitments in DCS allows us to validate and forward the column before the *exec payload arrives*, but we still have to wait for the beacon block to arrive.
- therefore, it does make sense to factor them out and lift them up to the execution bid, propagating them once instead of 128 times.
- this spares bandwidth generally, at the expense of adding 3KiB on the root object.
[2026-01-27T21:55:51.650000+00:00] raulvk: re: extra weight in the execution_payload_bid topic, <@755590043632140352>, it would be good to see calcs on the expected traffic. i assume it won't mirror the bid traces we see in MEV boost, as those are only feasible due to the point-to-point communication via a relay.
[2026-01-27T22:12:26.535000+00:00] jtraglia: Hey <@795439202732867594> flagging. I think there's a misunderstanding. Sidecar validation is dependent on receiving the bid from the previous slot. If we moved the commitments to `ExecutionPayloadBid`, this information would be available before any data column sidecar would be sent/received in the next slot.
[2026-01-27T22:32:15.821000+00:00] jtraglia: > therefore, it does make sense to factor them out and lift them up to the execution bid, propagating them once instead of 128 times
Yes, this is correct. But our concerns are primarily around bid latency. Will adding upwards of ~3500 bytes to the bid be too much? Keep in mind nodes [are expected to include some form of spam prevention](https://github.com/ethereum/consensus-specs/blob/46c1199d6b4584ba484dec807f03b8e6211dd725/specs/gloas/p2p-interface.md?plain=1#L322-L326). Could this change prevent the proposer from receiving bids in a timely fashion? I've estimated that it could introduce up to ~2ms of latency per hop or something like that.
[2026-01-27T22:48:08.524000+00:00] ralexstokes: i think we all agree 99(+?)% of all bids will come from point-to-point channels, so i think the extra 3.5k bytes isn't a huge deal
[2026-01-27T22:48:41.291000+00:00] ralexstokes: if im following correctly, its either a choice to put the burden on the network or put the burden on the builder(s)
[2026-01-27T22:53:42.258000+00:00] raulvk: Could you elaborate on “bid from the *previous* slot”?
[2026-01-27T23:05:38.199000+00:00] jtraglia: Afk now, but the payload and blobs are associated with the bid from the previous slot. The proposer selects the bid puts it in the state.
[2026-01-27T23:06:38.791000+00:00] jtraglia: So the builder broadcasts the payload/blobs after it sees that the proposer selected its bid. Right?
[2026-01-27T23:08:49.033000+00:00] potuz: Yeah that's a good framing. Just that the network is not really latency bound while builders are, not sure if the bid increase is enough to drive them away from including blobs
[2026-01-27T23:18:20.950000+00:00] potuz: <@795439202732867594> I think this analysis is based on current data which is far from valid on Gloas. Today the builder (or relay) is responsible from broadcasting of the full block, containing also the payload. This makes the block much larger and slow to propagate than columns while at the same time it makes it start propagating at the same time than columns! 

Neither of these assumptions are remotely reasonable on Gloas. In gloas the block is tiny, all transactions are gone, and the block propagates before the builder (that is reponsible for propagating blobs) even knows that they need to broadcast a payload + blob data. 

What can still be valid is that most blob transactions are there anyway in the mempool before the payload is even chosen. And this is what sending the KZG commitments in the bid (and consequently in the block) would solve: nodes will be able to start reconstructing data even before the person responsible to broadcast it knows that it needs to broadcast it. 

The question here is wheter this gain is worth the extra burden on the bidding p2p stack and the risk of extra latency on the builder connection that is sensitive to latency (the p2p stack is kinda irrelevant for latency). 

So just in case it wasn't clear: I think the assumption that we have already the beacon block to validate the data column sidecars is kinda irrelevant.
[2026-01-27T23:27:42.849000+00:00] raulvk: That’s not my understanding. Doesn’t bid for slot 3 commit to blobs that will be propagated during slot 3? And PTC for slot 3 attests to those blobs having been made available? I’m still confused about the reference to “previous”.
[2026-01-27T23:29:25.210000+00:00] __kasey__: BTW we could reduce the data a bit by using versioned hashes (32 bytes) instead of commitments (48).
[2026-01-27T23:37:01.244000+00:00] potuz: I think this is a good idea, that's all that's needed for the Payload validation anyway
[2026-01-27T23:37:35.767000+00:00] .paulharris: would scale better when we're sending tons of blobs too
[2026-01-27T23:38:09.689000+00:00] potuz: Otoh this will probably require a redesign of grtblobs and the engine way of storing them
[2026-01-27T23:39:35.320000+00:00] __kasey__: What would need to be redesigned? The versioned hash is already the input param to getBlobs.
[2026-01-27T23:40:17.744000+00:00] raulvk: Indeed, this is an advantage of ePBS. We expect the (slimmer) blocks to arrive sooner. What I’m trying to say is that, before, each column could be independently verified (as long as you had the parent block), so it made sense to include the commitments/proofs/etc. Now, the column cannot be verified standalone (it needs the block), so that reason is gone.
[2026-01-27T23:41:57.398000+00:00] potuz: Oh I thought this changed. If we only need to pass versioned hashes then yes there's no need for us to deal with commitments. Verifying the proofs in the DCs?
[2026-01-27T23:42:24.334000+00:00] raulvk: If we don’t have the KZG commitments we can’t open the cell proofs, right?
[2026-01-27T23:43:35.869000+00:00] potuz: Yes we can remove the commitments completely from the DCs if we include them in the bid.
[2026-01-27T23:44:28.219000+00:00] potuz: I still think the biggest con to this is the fact that it will make any form of slot auction or aps much harder
[2026-01-27T23:44:50.886000+00:00] potuz: But people aren't thinking this far ahead
[2026-01-27T23:44:51.261000+00:00] __kasey__: We'll have the full blobs and the proofs. We can construct the commitments from the full blob data, and later confirm they match byte-wise with the proposed values.
[2026-01-27T23:45:58.838000+00:00] raulvk: <@755590043632140352> do you expect to see much upbidding activity on the bids topic? My bet is that all bids from a builder for a slot would include the same blobs
[2026-01-27T23:47:07.896000+00:00] potuz: I don't think anyone knows what that topic will look like. I am not that worried about that topic cause we can heavily throttle it without much risk
[2026-01-27T23:47:55.595000+00:00] raulvk: IMO they might add/remove txs as their profit changes, but I’d expect blobs to stay static over the bidding war
[2026-01-27T23:47:57.888000+00:00] .paulharris: ok so the only place to get the list of blobs (versioned hashes)  would be in the  bid, so we can't just have a hash of them in the bid and get the list once the block is published... is that correct?
[2026-01-27T23:48:40.447000+00:00] potuz: Currently that's what we have, just a hash
[2026-01-27T23:48:44.206000+00:00] potuz: And it works
[2026-01-27T23:48:49.482000+00:00] potuz: But it has tradeoffs
[2026-01-27T23:49:03.391000+00:00] .paulharris: ok fair - just catching up still 🙂
[2026-01-27T23:51:10.342000+00:00] raulvk: Wasn’t that your main concern (“bidding p2p stack”), or are we talking about another stack?
[2026-01-27T23:51:33.896000+00:00] raulvk: (Sorry, still catching up with the many moving parts here)
[2026-01-27T23:52:43.307000+00:00] potuz: Oh yeah that's one of them. Other is the extra latency on the direct connections and the other is the fact that it enshrines a commitment to the Payload in the CL block (we currently have a  blockhash but we can remove it)
[2026-01-27T23:54:02.867000+00:00] potuz: I don't really know what the weight in bandwidth in the bidding topic it will be
[2026-01-27T23:54:17.269000+00:00] potuz: Don't even have intuition for it
[2026-01-27T23:55:39.897000+00:00] raulvk: I see. Personally, I don’t view the enshrinement as a problem. The CL is already aware of KZG commitments and blob specifics, so it’s just moving logic from one spot to another IMO. The “leakage” already exists.
[2026-01-27T23:57:05.495000+00:00] __kasey__: Currently, sidecars are self-contained for verification purposes, meaning the block can arrive later. If we removed the commitments from the sidecar, sidecar verification would be reliant on the bid (or the block which includes it) being seen first. Verifying column field elements wrt the commitments has to happen after we have those other values. In the case where we recover full blobs from the EL, this is ok, because we can recompute the commitment. But for missing blobs we are stuck waiting. Also when we move to cell level messaging, I assume this breaks down.
[2026-01-27T23:57:07.021000+00:00] potuz: Nah, the issue is that this constrains the Payload before it needs to be constructed in theory
[2026-01-27T23:59:01.882000+00:00] __kasey__: I meant this as a reply to potuz' comment that `we can remove the commitments completely from the DCs if we include them in the bid.`
```

</details>
