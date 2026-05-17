# Post-fork chain analysis and block processing

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around technical issues encountered during what appears to be the Gloas fork activation on a testnet. Initial problems included MEV-boost/relayer failures (503 errors), invalid parent block hash rejections in gossip messages, and a lack of available builders. Despite these early complications, the network appeared to recover with successful block processing and chain progression.

Multiple chain reorganizations occurred during the fork period, with Prysm attempting late block reorgs that were aborted due to attestation thresholds. The team observed unusual forkchoice behavior where some validators were voting on empty slots while others voted on different blocks, creating competing chains. However, the Gloas fork epoch eventually finalized successfully, and the network continued to finalize subsequent epochs normally across different client implementations (Lighthouse, Lodestar, Prysm).

The main unresolved issue was identifying which validators were consistently voting on empty slots, causing Prysm to attempt reorgs. Additionally, DNS issues prevented access to the Dora block explorer, limiting the team's ability to get a comprehensive view of the network state, though this was expected to resolve within 10 minutes as DNS caches refreshed.

## Participants

- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T15:04:09.433000+00:00] potuz: ```[2026-03-04 15:03:42.39] ERROR builder: Failed to call relayer status endpoint, perhaps mev-boost or relayers are down error=unsupported error code: 503: did not receive 200 response from API````
Had a twitch
[2026-03-04T15:04:54.076000+00:00] potuz: Something is broken already here
[2026-03-04T15:04:56.730000+00:00] barnabasbusa: ```
2026-03-04 15:04:23.583 WARN  - Rejecting gossip message on topic /eth2/dbd4b481/beacon_block/ssz_snappy, reason: Execution payload bid has invalid parent block hash 0x23471ca37c40621d327db6bca89315c06d15dc4d551df019682cc610da34e74d
```
[2026-03-04T15:04:59.795000+00:00] barnabasbusa: not looking good
[2026-03-04T15:05:15.955000+00:00] barnabasbusa: we dont have any builders
[2026-03-04T15:05:17.738000+00:00] potuz: we're requesting parents by root already
[2026-03-04T15:06:18.784000+00:00] potuz: do we even have the right config?
[2026-03-04T15:06:25.911000+00:00] potuz: I don't see an upgrade to Gloas
[2026-03-04T15:07:06.914000+00:00] barnabasbusa: 
[2026-03-04T15:07:18.163000+00:00] potuz: Yeah I see it at launch
[2026-03-04T15:08:23.072000+00:00] potuz: my bad, the fork block is fine according to us ```[2026-03-04 15:03:59.57]  INFO blockchain: Synced new block block=0x6444154d... blockHash=0x2f3b92b172ae builderIndex=18446744073709551615 chainServiceProcessedTime=26.843027ms epoch=5 finalizedEpoch=3 finalizedRoot=0xa6c77e3a... justif
iedEpoch=4 justifiedRoot=0xe0d13059... parentHash=0xb2b9ae3d04ab parentRoot=0xad39b55a... sinceSlotStartTime=577.13571ms slot=160 slotInEpoch=0 version=gloas
[2026-03-04 15:03:59.57]  INFO blockchain: Finished applying state transition attestations=1 blobKzgCommitmentCount=0 builderIndex=18446744073709551615 parentHash=0xb2b9ae3d04ab payloadHash=0x2f3b92b172ae slot=160 syncBitsCount=499
[2026-03-04 15:03:59.57] DEBUG sync: Send batch root request: No suitable peers roots=[0x4f6734e2e11ce97892143a1c326b223655fe2baff3cf5f72060b8de0e65567b2]
[2026-03-04 15:03:59.63]  INFO blockchain: Processed execution payload envelope blockHash=0x2f3b92b172ae blockRoot=0x6444154d8bb0 parentHash=0xb2b9ae3d04ab slot=160````
[2026-03-04T15:13:09.552000+00:00] potuz: We seem to be fine with LH at least 
````
[2026-03-04 15:11:47.06]  INFO blockchain: Synced new block block=0xe089a4af... blockHash=0xfd5bd7f8aca3 builderIndex=18446744073709551615 chainServiceProcessedTime=15.418871ms epoch=6 finalizedEpoch=4 finalizedRoot=0xe0d13059... justifiedEpoch=5 justifiedRoot=0x6444154d... parentHash=0xd3c507def337 parentRoot=0x577e4a42... sinceSlotStartTime=62.128575ms slot=199 slotInEpoch=7 version=gloas
[2026-03-04 15:11:47.06]  INFO blockchain: Finished applying state transition attestations=1 blobKzgCommitmentCount=0 builderIndex=18446744073709551615 parentHash=0xd3c507def337 payloadHash=0xfd5bd7f8aca3 slot=199 syncBitsCount=490
[2026-03-04 15:11:47.06] DEBUG sync: Verified and saved pending attestations to pool aggregateAttAndProofCount=0 attCount=5 blockRoot=0xe089a4afa6de6da3e7099d6214564c3d7ed033333469521dd66269d6c45dde1f durationAggregateAttAndProof=781ns durationAtts=3.558716ms durationTotal=3.559497ms totalCount=5 uniqueAggregateAttAndProofCount=0
[2026-03-04 15:11:47.06] DEBUG sync: Requesting blocks by root count=4 peer=16Uiu2HAmVnbTH9e82S9CD75a3DwWWVW6GojphSLaVxer3XwYYo65 roots=[0xc8cf040dabd9af48e43eede0f357b7bd7530b689ac8efae32c51bfb95157e2dd 0x596e0ef12e517fa930b69d033285f6bbe590492488fe987f6153f06c7f3b0386 0x9f42b156d780e7ee22b8ac6b7964c60df9e5e8526f633a2036c69b989c173d80 0xf94bf8eade1740fa6f770b8d9bec97bc77746fa2d5f01c8e7659f0e74d2b7170]
[2026-03-04 15:11:47.06] DEBUG sync: Fetched data column sidecars from storage finalMissingRootCount=0 initialMissingRootCount=0
[2026-03-04 15:11:47.08]  INFO blockchain: Processed execution payload envelope blockHash=0xfd5bd7f8aca3 blockRoot=0xe089a4afa6de parentHash=0xd3c507def337 slot=199````
[2026-03-04T15:14:05.469000+00:00] potuz: We just had a reorg
[2026-03-04T15:14:29.022000+00:00] potuz: ````
[2026-03-04 15:13:57.00]  INFO blockchain: Attempted late block reorg aborted due to attestations after threshold root=0x676e267542b0e7b1b076163dcc2ce15e839e83dbe3d02448b7754d85189d70c3 sinceSlotStart=10.001768884s threshold=10s weight=1944000000000
[2026-03-04 15:13:59.00] DEBUG blockchain: Head changed due to attestations newHeadRoot=0x8e58e99dad325af9095d2977622b325eaf0bb125e76c44d2bf67c2e66d447624````
[2026-03-04T15:14:58.537000+00:00] potuz: ````
[2026-03-04 15:14:09.00]  INFO blockchain: Attempted late block reorg aborted due to attestations after threshold root=0x0acf17b0312a06e49073b6f4a9c1aa009ee903e89d1ef9676d158b367cad27b3 sinceSlotStart=10.002265536s threshold=10s weight=1248000000000
[2026-03-04 15:14:09.00]  INFO blockchain: Chain reorg occurred commonAncestorRoot=0xb0a7cdeb1f787d1a1ec1fc49f583569821c7c5d2c38edbafcfd7ec8658e5e402 depth=4 distance=5 newRoot=0x0acf17b0312a06e49073b6f4a9c1aa009ee903e89d1ef9676d158b367cad27b3 newSlot=210 newWeight=1248000000000 oldRoot=0x8e58e99dad325af9095d2977622b325eaf0bb125e76c44d2bf67c2e66d447624 oldSlot=207 oldWeight=7392000000000
`````
Weird that blocks will come late
[2026-03-04T15:15:48.758000+00:00] potuz: ah no, there's already some many reorgs
[2026-03-04T15:16:19.608000+00:00] potuz: [2026-03-04 15:13:47.19] DEBUG sync: Received block blockSlot=209 graffiti=prysm-geth-2 GE57c3PR2232 proposerIndex=802 sinceSlotStartTime=188.797094ms validationTime=1.841241ms
[2026-03-04 15:13:47.20] DEBUG blockchain: Head block is not the received block headEmptyWeight=1408000000000 headFullWeight=1144000000000 headRoot=0x8e58e99dad325af9095d2977622b325eaf0bb125e76c44d2bf67c2e66d447624 headWeight=4408000000000 receivedRoot=0x676e267542b0e7b1b076163dcc2ce15e839e83dbe3d02448b7754d85189d70c3 receivedWeight=760000000000

I think this is a forkchoice issue on attestations for previous blocks
[2026-03-04T15:16:43.505000+00:00] terencechain: are all clients voting committee index=1 on skipped slot?
[2026-03-04T15:16:49.809000+00:00] terencechain: that'll make a huge diff
[2026-03-04T15:18:23.339000+00:00] potuz: yeah how to see the full picture without Dora and the forkchoice dump endpoint
[2026-03-04T15:20:04.711000+00:00] potuz: There are reorgs but I still see a chain, we follow on top of Lodestar and Lighthouse from time to time
[2026-03-04T15:22:30.179000+00:00] potuz: I see head correctly at slot 248 though
[2026-03-04T15:23:43.060000+00:00] potuz: And moreover I see 
```[2026-03-04 15:22:35.05]  INFO blockchain: Synced new block block=0x229601b1... blockHash=0xc0b1a4543737 builderIndex=18446744073709551615 chainServiceProcessedTime=20.113827ms epoch=7 finalizedEpoch=5 finalizedRoot=0x6444154d... justifiedEpoch=6 justifiedRoot=0xdf424ae5... parentHash=0x5093bd2bdc8c parentRoot=0x2ea03d95... sinceSlotStartTime=53.034146ms slot=253 slotInEpoch=29 version=gloas````
So The Gloas fork epoch is finalized
[2026-03-04T15:24:29.802000+00:00] potuz: And we keep finalizing 
```[2026-03-04 15:24:11.08]  INFO blockchain: Synced new block block=0x0f94ca06... blockHash=0x1b4201d972a0 builderIndex=18446744073709551615 chainServiceProcessedTime=18.42554ms epoch=8 finalizedEpoch=6 finalizedRoot=0xdf424ae5... justifiedEpoch=7 justifiedRoot=0x22d0ecee... parentHash=0x12e8c32d9968 parentRoot=0x56e1a2ac... sinceSlotStartTime=89.526092ms slot=261 slotInEpoch=5 version=gloas```
[2026-03-04T15:24:36.426000+00:00] potuz: Is this called success <@412614104222531604> ?
[2026-03-04T15:24:46.123000+00:00] terencechain: no dora, no success
[2026-03-04T15:24:54.032000+00:00] barnabasbusa: we have dns issues 😂
[2026-03-04T15:24:55.849000+00:00] barnabasbusa: psst
[2026-03-04T15:25:03.649000+00:00] barnabasbusa: completely unrelated
[2026-03-04T15:25:09.139000+00:00] potuz: We're finalizing fine it seems
[2026-03-04T15:25:21.605000+00:00] potuz: Still would want to understand who the hell is voting on empty
[2026-03-04T15:25:28.609000+00:00] potuz: cause it's making Prysm try to reorg them
[2026-03-04T15:25:38.766000+00:00] barnabasbusa: we should be ok in 10 mins
[2026-03-04T15:25:42.973000+00:00] barnabasbusa: https://dora.epbs-devnet-0.ethpandaops.io
[2026-03-04T15:25:57.260000+00:00] barnabasbusa: we had a messed up dns entry and cache gets flushed in ~10 mins
```

</details>
