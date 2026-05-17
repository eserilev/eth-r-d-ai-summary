# P2P Builder Implementation and Testing

**Channel:** epbs | **Date:** 2026-03-23

## Summary

Terencechain is looking to test P2P builder functionality including gossip for proposer preferences and bids, proposer selection of higher-value P2P bids over local ones, and payload release mechanics. They want to run these tests on Kurtosis or devnet 0.

Bharath.vedartham confirms that the existing [buildoor](https://github.com/ethpandaops/buildoor) tool is available but currently lacks proposer preference support, though this could be implemented quickly. They note that full implementation would likely require a new beacon API ([PR #584](https://github.com/ethereum/beacon-APIs/pull/584)) for builders to construct execution payload envelopes, as this involves post-state beacon transitions. Buildoor has been tested pre-Gloas on Kurtosis and can deliver payloads via the builder API.

The team appears flexible on implementation approach - terencechain suggests they could proceed without the beacon API using a lightweight builder approach instead. The functionality is confirmed to be available in their devnet0 branch, with bharath offering to quickly implement the missing proposer preferences listening capability to unblock testing.

## Participants

- bharath.vedartham
- nflaig
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-23T18:06:11.987000+00:00] terencechain: do we have a p2p builder implemented today? for kurtosis or even devnet 0 i want to test the following:
- p2p gossip for proposer preference
- p2p gossip for bids
- proposer uses the p2p bid when its value is higher than local bid
- p2p builder releases the payload after seeing the proposer includes its bid
[2026-03-23T18:33:46.341000+00:00] bharath.vedartham: we have https://github.com/ethpandaops/buildoor but it doesn't support reading proposer preferences yet. we can do that pretty easily shouldn't be a lot of work. 
I think we will need this API in a client to implement an external builder: https://github.com/ethereum/beacon-APIs/pull/584 this api is needed for the builder with the payload and execution requests to construct an execution payload envelope as the envelope construction involves a post state beacon state transition
[2026-03-23T18:34:57.105000+00:00] bharath.vedartham: does prysm support all of these things now? if so, I can work on implementing these things quickly so that you can be unblocked on testing.
We can implement listening to the proposer preferences topic pretty fast
[2026-03-23T18:36:00.334000+00:00] bharath.vedartham: buildoor has been tested pre-gloas on a local kurtosis testnet and delivers payloads via the builder-api. so i believe we would just need to link up the machinery to get it to create the bids and submit them
[2026-03-23T18:44:51.267000+00:00] nflaig: https://discord.com/channels/595666850260713488/1483497365594832917 for visibility, relevant for proposer preferences discussion above
[2026-03-23T20:12:54.952000+00:00] terencechain: i dont think we have the beacon api, but i was thinking we dont even need the beacon api, we could just having a light weight builder that does it.  I dont have a preference which direction we take
[2026-03-23T20:13:15.661000+00:00] terencechain: its in our devnet0 branch!
```

</details>
