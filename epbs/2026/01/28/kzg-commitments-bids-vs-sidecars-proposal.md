# Data Column Self-Contained Validation and KZG Commitments

**Channel:** epbs | **Date:** 2026-01-28

## Summary

The discussion centers on a fundamental change in data column (DC) validation under the new ePBS (enshrined Proposer-Builder Separation) specification. Previously, data columns were self-contained and could be validated independently without requiring the beacon block, as they included all necessary components: cells, cell proofs, KZG commitments, inclusion proofs, and signed block data. However, under the current ePBS spec, this self-contained validation is no longer possible because DCs are broadcast by builders (only known after payload revelation) and lack the inclusion proofs, creating a strict dependency on receiving the beacon block first.

The core technical debate focuses on where to place KZG commitments - either in the execution payload bid (increasing bid size from ~212 bytes to ~3600 bytes for 72 blobs) or keeping them in the DCs and execution payload as currently specified. Potuz created [issue #4870](https://github.com/ethereum/consensus-specs/issues/4870) and [PR #4875](https://github.com/ethereum/consensus-specs/pull/4875) proposing to move commitments to bids, which would remove ~448KB from broadcast data while adding ~3.4KB to bids. Builder representatives expressed concern about the bid size increase affecting network segmentation (preferring single-packet ~1400 byte bids), though they noted that builders already include commitments in current builder API implementations and are unlikely to omit blobs to save bandwidth.

The discussion reached a tentative consensus favoring moving KZG commitments to bids, as it would significantly simplify DC validation for client implementations like Prysm, reduce overall network bandwidth, and align with existing builder API practices. However, concerns remain about the impact on auction dynamics and P2P performance, with builders noting the transition from current "hydration/v3" optimizations to potentially paying full latency costs for larger bids.

## Participants

- 0w3n6325
- __kasey__
- bharath.vedartham
- jtraglia
- l03443
- nflaig
- potuz
- ralexstokes
- raulvk

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-28T00:01:08.144000+00:00] raulvk: But even keeping the KZG commitments in the DCS, sidecars were no longer self-contained in the current ePBS spec
[2026-01-28T00:02:47.917000+00:00] raulvk: Before: column => cells => cell proofs => KZG commitments => inclusion proof => signed block => execution payload root. All this was present and verifiable in the column.
[2026-01-28T00:04:01.274000+00:00] __kasey__: For the purposes of DA we stop caring at `signed block`.
[2026-01-28T00:04:09.584000+00:00] raulvk: That is no longer the case, so KZG commitments being there are kinda an accidental limbo IMO. Not in the bid, not in the exec payload, no self-contained verifiability while being in DCS
[2026-01-28T00:05:48.611000+00:00] raulvk: The point is, before you could verify, accept and forward a DCS without having received the beacon block. Now you can’t (and we think it’s fine because the block is slimmer, so it’ll propagate faster)
[2026-01-28T00:07:03.734000+00:00] __kasey__: I think we are saying the same thing. I was pointing it out as an implication of the change. It moves these things to strictly happening in serial.
[2026-01-28T00:07:09.505000+00:00] potuz: Not only slimmer: the DCs can't even start being broadcast until the block has
[2026-01-28T00:07:09.763000+00:00] jtraglia: Not exactly. We need the bid in the sidecar gossip checks: https://github.com/ethereum/consensus-specs/blob/46c1199d6b4584ba484dec807f03b8e6211dd725/specs/gloas/p2p-interface.md?plain=1#L385-L387
[2026-01-28T00:08:05.670000+00:00] __kasey__: Ah thanks.
[2026-01-28T00:08:32.539000+00:00] potuz: Yes this will always be the case cause we need to be validating for an expected builder and this cannot be known without the block
[2026-01-28T00:09:10.076000+00:00] potuz: But the point is that the DCs can't even start being broadcast without the CL block already having been broadcast
[2026-01-28T00:09:25.309000+00:00] potuz: The only edge case is self building and I don't care much about that
[2026-01-28T00:22:43.738000+00:00] raulvk: There’s higher latency between the block and the DC sends now, yes, as they no longer come from the same node, and the block needs to travel some p2p network path before the builder realizes its bid was selected and proceeds to release the DCs
[2026-01-28T01:14:58.567000+00:00] potuz: I don't think you can compute the commitments without having all the data right? if you only get a DCs and you do not have the commitments you cannot reproduce them from neither the data nor the versioned hashes. I'll create an issue with what I see as the tradeoff and viable approaches, I do not know how to make the versioned hash version work so I'll leave that just as a comment
[2026-01-28T01:21:46.500000+00:00] potuz: <@795439202732867594> <@833706406699073536> created this issue that may be a more permanent place to have a discussion there https://github.com/ethereum/consensus-specs/issues/4870
[2026-01-28T01:23:10.244000+00:00] __kasey__: I was talking about the full blob. Which is why I mentioned it breaks down as we move to cell level on the EL.
[2026-01-28T01:25:43.678000+00:00] potuz: But the DCs don't have the blob data anyway
[2026-01-28T01:25:59.048000+00:00] potuz: How do you verify the DCs if you don't have the commitments
[2026-01-28T01:26:22.035000+00:00] __kasey__: The EL has the full blob, so it could compute the commitment.
[2026-01-28T01:26:33.038000+00:00] potuz: The EL may have nothing
[2026-01-28T01:26:44.288000+00:00] potuz: You get a DCs how do you verify it?
[2026-01-28T01:30:34.753000+00:00] __kasey__: Sorry I was trying to imply all this earlier but I think it wasn’t clear. Yes something needs the full commitments. That could be the block, or it could be the bid. If it’s in the bid, it’s in the block too. Or put the versioned hashes in the bid and the full commitments in the block. In either case, if the EL has the blob, it can generate commitments, if it doesn’t, you have to wait for the block (which is what started the thread above clarifying self-contained data sidecar verification).
[2026-01-28T01:31:17.664000+00:00] __kasey__: What doesn’t work is only versioned hashes in the bid. In that case you are hosed without the blob in EL.
[2026-01-28T01:32:17.848000+00:00] potuz: Yeah ok then we're in the same page, the only two designs I can see are the ones I wrote in the issue then: move commitments to the bid and remove them entirely from everywhere else, or what we have now in which commitments are in the DCs and the Payload
[2026-01-28T01:32:27.329000+00:00] potuz: But not in bids
[2026-01-28T01:33:31.693000+00:00] potuz: I think this trade-off was discussed years ago and we may have rushed at assuming that adding them to the bids was worse.
[2026-01-28T09:26:16.946000+00:00] bharath.vedartham: This would apply if the bid came via the p2p bidpool as the builder would have to listen to the beacon block via gossip. 
But if the bid came via the Builder-API, the proposer returns the beacon block back to the builder on a direct TCP connection if it includes the builders bid. Post which the builder can release the payload with the DCs. So the gap b/w the beacon block and DC sends can be almost neglible on the Builder-API. 

One reason why there can be a gap b/w block and DC sends could be that the builder waits for the block to get enough attestations before releasing it in order to avoid a re-orged payload
[2026-01-28T09:26:51.600000+00:00] bharath.vedartham: w.r.t the Builder-API, it wouldn't matter if the bids are in the commitment as today the commitments are already sent in the bid: [BuilderBid](https://github.com/ethereum/builder-specs/blob/13bec3d8e74bf7570922b3735a79ad0a53fcc9a5/specs/electra/builder.md#L33)
[2026-01-28T09:29:41.013000+00:00] potuz: The commitments are not sent in the bid
[2026-01-28T09:30:30.312000+00:00] potuz: You made a different type for the builder API? Yeah that PR needs to be reviewed
[2026-01-28T09:30:41.592000+00:00] potuz: That doesn't make sense
[2026-01-28T09:31:18.200000+00:00] bharath.vedartham: sorry this is not the BuilderBid sent in gloas. This is the BuilderBid sent today with Fulu
[2026-01-28T09:31:24.050000+00:00] potuz: That link is for the type today
[2026-01-28T09:31:33.384000+00:00] bharath.vedartham: in gloas the builder just sends the `SignedExecutionPayloadBid`
[2026-01-28T09:31:55.335000+00:00] potuz: Yes that's more reasonable and that bid doesn't have the commitments today
[2026-01-28T09:32:45.732000+00:00] bharath.vedartham: yes the point i was trying to make was that adding the commitments to the `SignedExecutionPayloadBid` shouldn't be a problem w.r.t to the Builder-API.
[2026-01-28T09:33:11.747000+00:00] bharath.vedartham: as the builder already sends the commitments as part of the bid today
[2026-01-28T10:42:38.854000+00:00] raulvk: Right, this settles it for me then.
- we can no longer validate columns without the block, so kzg comms on every column are dead weight
- we expect builders to keep using the builder API, which already includes the full list of KZG commitments on every bid. We should readd it for Gloas.
- we are unsure about how the p2p bid channel will be used, so it feels premature to optimize for compactness there
[2026-01-28T10:53:15.077000+00:00] raulvk: Related: this is nice. Repeating the signed beacon block + kzg commitments + inclusion proof on every column was overhead, but worthwhile because it bought us self-contained validation. In a world of heavier, variably sized blocks this was a desirable property. Now that we have lighter and stably sized blocks, it seems more viable to depend on the block, but we’ll need to carefully study and optimize at the p2p layer to derisk this new data flow dependency (alas, we also have longer slack to propagate blobs, for now) <@477396362917576713>
[2026-01-28T11:50:25.494000+00:00] potuz: <@&1458826346896687229> could any of you weigh in the effects of having or not having the full KZG list in the bids? keep in mind the difference between 9-15 blobs to 72-128 blobs.
[2026-01-28T11:51:04.246000+00:00] potuz: And the changes in the auction mechanism from a push-to-relay system to a one-shot pull-from-proposer one
[2026-01-28T12:03:12.106000+00:00] potuz: <@586161934425128960>, re: your comment on the issue, I second that, we implemented ePBS on top of electra, and did so without any blobs because I envisioned that not having the commitments in the block body was going to be a big problem for Prysm. This would be a big saving in complexity for us if we were to move the commitments to the bid (even though I am scared that it hurts ABS/APS).
[2026-01-28T12:04:03.430000+00:00] l03443: I'm trying to fully understand the tradeoff but as general rule the less data we have in the hot path (bid from builder -> proposer) the better it is. Ideally it could fit a single packet, ~1400 bytes
[2026-01-28T12:09:01.244000+00:00] potuz: The current specification has them at 212 bytes, it would go to about 3600
[2026-01-28T12:10:31.618000+00:00] l03443: we put some thoughts here: https://titanbuilder.substack.com/i/167800100/auction-dynamics , essentially moving to a first price blind auction . though relays are still free to implement alternative auction mechanisms
[2026-01-28T12:11:10.413000+00:00] l03443: that seems a lot of overhead to pay for every single bid. What would be the tradeoff if the commitments are not there?
[2026-01-28T12:11:40.725000+00:00] potuz: yeah I meant only this particular change in this context, how much of an impact would be and if builders would be incentivized to not produce blobs because of this. Notice that estimations from <@592004585506340885> are just 2ms not much more. We're talking from 1 packet to 2-3.
[2026-01-28T12:12:20.648000+00:00] potuz: https://github.com/ethereum/consensus-specs/issues/4870
Describes the issue well I think
[2026-01-28T12:13:23.961000+00:00] potuz: It removes about 448000 bytes from the data being broadcast by adding these 3500  to the bid
[2026-01-28T12:27:00.050000+00:00] nflaig: we already include `blob_kzg_commitments` currently, and with gloas we safe a lot by omitting `execution_payload_header` and `execution_requests` which currently the worst case bid size can be ~1.7 MB ssz encoded, there was some discussion [here](https://github.com/ethereum/builder-specs/issues/120)
[2026-01-28T12:27:43.418000+00:00] bharath.vedartham: Does using only SSZ encoding over the wire help reduce the size of the bid?
[2026-01-28T12:28:03.453000+00:00] nflaig: ~50% smaller compared to using json
[2026-01-28T12:28:04.964000+00:00] potuz: In my static numbers from 212 bytes to 3600 I am assuming SSZ
[2026-01-28T12:28:57.916000+00:00] potuz: I think it's useless to use the "we send the bid today" kinda argument anyway, the fact that the bid today is 1.7MB is irrelevant what matters is the difference with and without this feature at the given fork
[2026-01-28T12:30:20.835000+00:00] potuz: the overhead we pay on the bid is 3500 bytes over 212 (plus the signature that I am not counting so reallistically it should be ~300 bytes)  and the question is if this overhead is relevant. It should be noted that perhaps the relative nature is not so important since in absolute value it may not mean any extra latency
[2026-01-28T12:50:14.770000+00:00] 0w3n6325: It's hard to predict future builder behavior but I can't imagine builders are likely to omit a blob just to decrease data transfer on the bid path by 48 bytes. The saving is much higher now and they don't really do it. Partially due to social pressure. Builders don't want to be perceived by the community to be censoring blobs.

For example in this block titan submitted a bid to our relay with 14 blobs. The "bid" here is the full block so they had to include 14 * ~131.1 kb

https://beaconcha.in/block/24333391#blobs
[2026-01-28T13:41:07.764000+00:00] raulvk: shameless plug for the blob flow viz (refreshes daily) on the p2p observatory: https://observatory.ethp2p.dev/latest/blob-flow we should be able to enhance to show builders as origins
[2026-01-28T13:43:49.255000+00:00] raulvk: Re: the 1400 bytes threshold. Is this because some systems use datagrams, or because you're concerned about segmentation?
[2026-01-28T14:02:59.147000+00:00] jtraglia: To be clear, 3500 bytes is for 72 blobs. Currently, the max added to the bid could be 1008 bytes.
[2026-01-28T14:03:27.812000+00:00] l03443: i think one point here is that now the builders hardly pay the full latency cost thanks to hydration / v3. We did use to see a lot lower blob counts before, especially during high volatility
[2026-01-28T14:03:36.016000+00:00] l03443: segmentation indeed
[2026-01-28T18:20:13.064000+00:00] potuz: Related to the above, this is how it would look if we move kzg commitments to the bid
https://github.com/ethereum/consensus-specs/pull/4875
It would simplify a lot of things on data column validation for Prysm
[2026-01-28T18:34:00.288000+00:00] ralexstokes: i looked at this a bit with Raúl; given what i know right now if we are going to touch the DataColumn structure at all this would be my preference
[2026-01-28T18:35:03.524000+00:00] ralexstokes: the place it could get weird is if builders are sending many updated bids over a critical period to proposers, although Raúl made the point there's little reason for the blobs to be changing so the commitment list would be static for this entire exchange
[2026-01-28T18:35:59.742000+00:00] ralexstokes: changing the structure at all implies we now have a strict dependency of the DataColumns against the BeaconBlock, which we didn't have before (at the cost of a larger DataColumn message on the wire)
[2026-01-28T19:52:07.084000+00:00] potuz: We always have a strict dependency of the DCs on the block
[2026-01-28T19:52:21.541000+00:00] potuz: there's no way to validate them without having a bid saying that the beacon block included them
[2026-01-28T19:52:35.258000+00:00] potuz: so regardless of this change, the DCs cannot be validated without seeing the beacon block before
[2026-01-28T19:53:47.449000+00:00] potuz: I am a little pissed cause if the bid increase is not bad for P2P the only reason I have to advocate against this change is because I believe it's also a nobrainer to remove entirely the auction and just have the protocol charge builders and it's trivially implementable. This change would make it a little harder, but not so much
[2026-01-28T19:54:12.844000+00:00] potuz: The benefits for validations are a no brainer, and it saves a lot of bwidth in the way
[2026-01-28T22:28:37.450000+00:00] ralexstokes: right but today DCs are self-contained, and it looks like we lost that property in the current gloas specs
[2026-01-28T22:29:03.960000+00:00] ralexstokes: we could just keep the status quo DC layout and keep it as-is
[2026-01-28T22:29:15.595000+00:00] ralexstokes: im not sure why it was changed in the first place actually
[2026-01-28T22:35:19.677000+00:00] potuz: We can't, the inclusion proof is not possible cause they aren't part of the block body and the DCs are broadcast by an entity that is only known after the Payload has been revealed
[2026-01-28T22:36:07.948000+00:00] potuz: So on any universe in which the builder is responsible for sending the DCs we will need to have seen the block before we can validate them
[2026-01-28T22:37:26.873000+00:00] potuz: Well I'm lying in fact we could do a construction in which there is a root committed in the bid and the builder sends a Merkle proof against that bid
[2026-01-28T22:37:30.493000+00:00] potuz: But that is crazy
[2026-01-28T22:44:22.839000+00:00] ralexstokes: you say tomato, i say tomato
[2026-01-28T22:45:14.984000+00:00] potuz: Lol any way even in these crazy schemes if it happens that you get the column before the block you should stop your node
[2026-01-28T22:45:18.429000+00:00] potuz: And move country
[2026-01-28T22:57:22.781000+00:00] ralexstokes: lol
```

</details>
