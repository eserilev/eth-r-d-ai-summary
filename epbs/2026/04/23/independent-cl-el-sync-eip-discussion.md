# Independent CL/EL Sync EIP Discussion

**Channel:** epbs | **Date:** 2026-04-23

## Summary

This discussion centers around an EIP for Independent CL/EL Sync proposed by potuz, with particular focus on how it would affect the Nimbus client. The EIP proposes using SHA256 hashing instead of SSZ `hash_tree_root` to avoid potential bikeshedding arguments with EL developers, despite SSZ being technically simpler for the CL side. Tersec notes that EL clients already have SSZ implementations for light clients, but potuz prioritizes avoiding extended debates over what should be a minor change.

The main concern was whether independent range syncing would negatively impact Nimbus, which currently stores execution payloads in its database rather than using a "blinded" approach like Lighthouse. The Nimbus team (0xshum and tersec) confirmed they should be fine with the range syncing changes as long as the Envelope isn't required in state transitions. However, they identified a potential issue where Nimbus would need to implement fetching payloads from the EL for gossip requests, since their envelope database would be empty for range-synced blocks.

The discussion concludes that the EIP heavily favors blinded storage schemas and essentially requires them, which aligns well with the ePBS (enshrined PBS) direction where CL blocks are inherently similar to blinded beacon blocks. Potuz notes that after implementing this change, they plan to drop explicit blinding since it becomes unnecessary in the ePBS world.

## Participants

- 0xshum
- m.kalinin
- potuz
- tersec
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-23T06:53:20.262000+00:00] m.kalinin: Add EIP: Independent CL/EL Sync by potuz...
[2026-04-23T11:42:48.276000+00:00] potuz: <@654267572107083777> <@822741226088169479> Can you say something about how this EIP would affect negatively nimbus? would this really be a problem for you?
[2026-04-23T11:45:53.679000+00:00] tomi0x: looking into it. cc <@1305213827398500432>
[2026-04-23T12:07:45.729000+00:00] tersec: > SHA256 is chosen over `hash_tree_root` because the execution layer does not currently implement SSZ hashing infrastructure. Using plain SHA256 over the concatenation of serialized request data allows both layers to compute the accumulator using only primitives they already have. The accumulator does not need to be Merkle-provable; it only needs to provide a binding commitment to the chain of values

...
[2026-04-23T12:07:58.567000+00:00] tersec: more random ad hoc jankiness but whatever
[2026-04-23T12:08:15.049000+00:00] tersec: also they _do_ have SSZ
[2026-04-23T12:08:16.382000+00:00] potuz: don't want to get into a neverending argument with EL devs about SSZ
[2026-04-23T12:08:23.511000+00:00] tersec: geth has a light client
[2026-04-23T12:08:29.639000+00:00] tersec: so does erigon
[2026-04-23T12:08:46.947000+00:00] tersec: Erigon bragged about writing 8 SSZ impls for different clients in a weekend
[2026-04-23T12:08:53.916000+00:00] potuz: the reason for the ping is a different one: I was told that doing independent range sync would hurt Nimbus.
[2026-04-23T12:09:04.268000+00:00] tersec: ok, looking at that part
[2026-04-23T12:09:23.317000+00:00] potuz: If I add SSZ to that EIP it's already going to be an infinite bike-shedding argument over something that should be a very minor change
[2026-04-23T12:09:57.024000+00:00] potuz: of course SSZ would be much much easier and simpler on our side
[2026-04-23T12:12:37.254000+00:00] 0xshum: I think Nimbus should be fine for independent range sync. As long as Envelope is not required in the state transition, we should be good to progress.
[2026-04-23T12:13:30.118000+00:00] potuz: ah nice, thanks!
[2026-04-23T12:14:16.715000+00:00] potuz: I was told that you guys did locked step syncing and that would be more complicated cause you'd need to get the payloads from the EL instead of the CL
[2026-04-23T12:14:35.976000+00:00] potuz: as the idea would be to completely remove range syncing of payloads from the CL as not-necessary
[2026-04-23T12:14:43.883000+00:00] tersec: well we want to be able to
[2026-04-23T12:14:51.737000+00:00] tersec: but right now it's opt sync like everyone else
[2026-04-23T12:15:08.160000+00:00] potuz: this is separate than optimistic syncing actually
[2026-04-23T12:15:33.417000+00:00] potuz: after catching up to head and continue syncing  while gossiping, the CL may still be optimistic
[2026-04-23T12:15:55.385000+00:00] tersec: ok, sure
[2026-04-23T12:15:58.514000+00:00] potuz: but the point is to remove payload downloading from range syncing and only download payloads over gossip
[2026-04-23T12:17:09.037000+00:00] tersec: one issue is that it'd leave (cc <@1305213827398500432> is this true?) Nimbus's envelopes database empty for the intervening blocks
[2026-04-23T12:17:34.638000+00:00] tersec: and we never did the Lighthouse-style passthrough of payload to EL for req/resp etc
[2026-04-23T12:18:01.151000+00:00] tersec: so if the protocol then requires the CL to produce the envelopes then this means now Nimbus has to have a way of fetching them from the EL
[2026-04-23T12:18:08.538000+00:00] tersec: even if it's not part of state transition
[2026-04-23T12:18:47.921000+00:00] potuz: So I believe the CL needs to produce the payloads for unfinalized sections in case it's requested by root at gossip time
[2026-04-23T12:19:05.136000+00:00] potuz: We should not downscore peers that don't have them though
[2026-04-23T12:19:09.018000+00:00] potuz: so it should be fine
[2026-04-23T12:19:14.749000+00:00] tersec: well
[2026-04-23T12:19:49.208000+00:00] potuz: clients should only request payloads by root at the tip of the gossip chain
[2026-04-23T12:19:53.634000+00:00] tersec: sounds like something that will be rolled back when some edge case or LH decides to do it differently
[2026-04-23T12:20:05.899000+00:00] 0xshum: For the range syncing part we should be fine, starting from gossip and byroot, we may need to do thing different, probably.
[2026-04-23T12:20:26.391000+00:00] tersec: yeah the _full CL syncing_ part looks ok
[2026-04-23T12:20:32.174000+00:00] tersec: it's everything around it
[2026-04-23T12:20:42.013000+00:00] potuz: what is the LH style passthrough of payloads to EL?
[2026-04-23T12:20:56.560000+00:00] tersec: at least at one point I'm told they didn't store execution payloads in their own database
[2026-04-23T12:21:00.264000+00:00] tersec: but requested them from the EL
[2026-04-23T12:21:02.181000+00:00] tersec: even pre-ePBS
[2026-04-23T12:21:22.859000+00:00] tersec: at least not permanently/for block retention time
[2026-04-23T12:21:27.133000+00:00] potuz: we do that as well, we store blocks blind
[2026-04-23T12:21:28.307000+00:00] tersec: so when a req/resp comes in, they fetch it from the EL (or did)
[2026-04-23T12:21:38.619000+00:00] tersec: right, well, Nimbus hasn't done that
[2026-04-23T12:21:57.628000+00:00] tersec: it's probably easier not to than to do now with ePBS though
[2026-04-23T12:22:10.688000+00:00] tersec: we played with it, it never quite made sense in our context
[2026-04-23T12:23:12.719000+00:00] potuz: oh I see so your fear is that you get requested those and since you haven't blinded you would need to implement that to serve, but yeah, as I said, you do not need to change anything here, the payloads that you receive over gossip you can keep and prune whenever you like and you need to serve them by root if requested. The range ones you never need to serve
[2026-04-23T12:23:28.180000+00:00] tersec: yeah, basically it heavily favors the blinded schemas
[2026-04-23T12:24:06.960000+00:00] tersec: more or less requires them
[2026-04-23T12:25:03.042000+00:00] tersec: the question is basically whether that's the only sane thing anyway in eBPS-world
[2026-04-23T12:25:05.624000+00:00] potuz: I don't think so, in fact after we do this we will drop blinding cause it becomes useless
[2026-04-23T12:27:52.392000+00:00] tersec: the CL blocks per se are similar in spirit to "blinded" pre-ePBS beaconblocks, no?
[2026-04-23T12:27:59.297000+00:00] tersec: not identical, sure
[2026-04-23T12:29:35.810000+00:00] tersec: so that's what I mean by, the default ePBS flow is kind of blinded
[2026-04-23T12:29:43.016000+00:00] tersec: it doesn't need explicit blinding anymore
```

</details>
