# Payload Serving and Sync Issues

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around a payload serving and synchronization issue on what appears to be a development network. The core problem is that nodes are unable to sync because no peers can serve the required execution payloads. Potuz explains that while clients other than Prysm don't yet support `ExecutionPayloadEnvelopesByRange` on the devnet branch, Prysm nodes are isolated and don't have the payloads either, creating a situation where "no one can serve us the data to sync."

Multiple attempts to restart nodes and sync have failed, with both Potuz and Terence unable to successfully sync their nodes. The issue appears to be compounded by connectivity problems, as the teams note they may not even be pairing with each other's nodes properly. Nflaig mentions they're working on adding the missing functionality but can't restart their nodes, while expressing uncertainty about whether nodes can sync back from a few epochs behind.

0xunclebill from what appears to be the Lighthouse team clarifies that while they don't have range sync implemented, they should be able to serve envelopes by range to peers. They note being disconnected from Prysm peers but without banning them, and commit to testing locally by stopping a Prysm node to investigate why Lighthouse isn't serving the envelopes as expected.

## Participants

- 0xunclebill
- nflaig
- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T18:57:37.397000+00:00] parithosh: lemme know if you need more data or something
[2026-03-04T18:57:58.971000+00:00] potuz: I don't think so
[2026-03-04T18:58:10.041000+00:00] potuz: it'd be nice to get a node to serve the payloads
[2026-03-04T18:58:38.692000+00:00] potuz: so that we catch up, with the PR that is in develop we would not have downscored peers
[2026-03-04T19:06:40.365000+00:00] parithosh: queried all the nodes though, as per the RPC no one has the payload to serve it?
[2026-03-04T19:07:35.374000+00:00] potuz: I think clients other than Prysm do not support yet ExecutionPayloadEnvelopesByRange on the devnet branch
[2026-03-04T19:07:45.701000+00:00] potuz: and Prysm doesn't have the payloads cause it's isolated
[2026-03-04T19:07:51.676000+00:00] potuz: so no one can serve us the data to sync
[2026-03-04T19:08:14.243000+00:00] nflaig: yeah we don't have them, working on adding them but we can't restart the nodes 😁
[2026-03-04T19:08:56.858000+00:00] nflaig: maybe we can try, I haven't actually tried if the node can sync back to head from few epochs back, likely not
[2026-03-04T20:48:36.874000+00:00] 0xunclebill: i dont see why we wouldnt be able to serve envelopes by range. dont have a great way to test this locally against LH nodes at the moment though
[2026-03-04T21:01:17.404000+00:00] potuz: stop a node and restart it?
[2026-03-04T21:01:29.393000+00:00] potuz: Terence tried and I tried and we both can't sync
[2026-03-04T21:01:37.746000+00:00] potuz: we don't even pair with you perhaps that's why
[2026-03-04T21:02:51.806000+00:00] 0xunclebill: we dont have range sync implemented, but we can serve envelopes by range to peers
[2026-03-04T21:03:08.491000+00:00] 0xunclebill: were disconnected from the 3 prysm peers, but doesnt seem like they've been banned
[2026-03-04T21:03:56.603000+00:00] 0xunclebill: ill try stopping a prysm node locally and see why lh isnt serving the envelopes
```

</details>
