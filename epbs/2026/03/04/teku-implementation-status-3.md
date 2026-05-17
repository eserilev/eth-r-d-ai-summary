# Chain stability assessment and Teku block validation issues

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around chain stability issues during an Ethereum devnet test, with a critical problem identified in Teku's block production. Potuz discovered that Teku produced an invalid block at slot 281 with a "bid parent block hash mismatch" error, where the block attempted to build on top of an older block (slot 224) rather than the current chain head. This caused a chain split, though the network continued finalizing blocks and other clients (Lighthouse, Prysm, Lodestar) remained synchronized on the main branch.

The team confirmed that Nimbus appears stuck at slot 160 while Teku has limited validator participation. Potuz observed some instances where Prysm blocks were reorged despite potentially being correct, suggesting possible fork choice implementation issues in other clients. However, he noted it's too early to diagnose these fork choice problems definitively and plans to wait a few days before focusing on that analysis.

Stefan (from Teku team) acknowledged they had made fixes after the devnet started and confirmed they may have been producing invalid blocks. The discussion also touched on development workflow improvements, with suggestions to use consistent branch naming schemes (like "epbs-devnet-0") for easier coordination across teams. A block explorer (Dora) was made available for monitoring the devnet status.

## Participants

- barnabasbusa
- potuz
- qu0b
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T15:32:31.479000+00:00] potuz: We seem to be absolutely fine guys, Can I open a bottle? or is it too early?
[2026-03-04T15:32:47.791000+00:00] potuz: ahhh crap
[2026-03-04T15:32:54.136000+00:00] potuz: we broke as I speak
[2026-03-04T15:33:19.666000+00:00] potuz: now we're really broken
[2026-03-04T15:33:49.806000+00:00] potuz: ```[2026-03-04 15:28:11.07] DEBUG sync: Gossip message was rejected agent=teku/teku/v22.1.1+3332-g57044e30f0/linux-x86_64/-eclipseadoptium-openjdk64bitservervm-java-21 error=could not get pre state for slot 281: could not process block: could not process execution payload bid: bid consistency validation failed: bid parent block hash mismatch: got d1424c208f74aec1cd64c25f18745bc964b2f4bdbe075fe9c9d3d890d125973d, expected 03611a4c6839eb3dc9ca69c49a26b387e3a3a3a6ce951086620b55d723a5c6aa gossipScore=27.783451215481808 multiaddress=/ip4/116.203.62.101/tcp/9000 peerID=16Uiu2HAm2NURgQurfHHwXLP8XAFJsFb5R7LVGhtDdjAQiCbdK4Cp topic=/eth2/dbd4b481/beacon_block/ssz_snappy````
[2026-03-04T15:35:17.942000+00:00] potuz: Teku produced a garbage block it seems <@577788234810130432> ```[2026-03-04 15:28:11.07] DEBUG sync: Could not validate beacon block error=could not get pre state for slot 281: could not process block: could not process execution payload bid: bid consistency validation failed: bid parent block hash
mismatch: got d1424c208f74aec1cd64c25f18745bc964b2f4bdbe075fe9c9d3d890d125973d, expected 03611a4c6839eb3dc9ca69c49a26b387e3a3a3a6ce951086620b55d723a5c6aa graffiti=teku-geth-1 GE57c3TK5704 proposerIndex=1826 slot=281 version=7````
[2026-03-04T15:35:28.622000+00:00] potuz: That sends the chain now on shackles
[2026-03-04T15:35:40.967000+00:00] potuz: it tries to build on top of 224 as far as I can tell
[2026-03-04T15:36:08.735000+00:00] potuz: It is consistent with what <@412614104222531604> found before of Teku building on top of old blocks
[2026-03-04T15:36:40.644000+00:00] potuz: would love confirmation from other teams if they can actually sync that block 281
[2026-03-04T15:37:18.513000+00:00] potuz: Good news is we're finalizing still
```[2026-03-04 15:36:35.09]  INFO blockchain: Synced new block block=0x9ca431b1... blockHash=0x03ec7c6b614f builderIndex=18446744073709551615 chainServiceProcessedTime=20.305573ms epoch=10 finalizedEpoch=8 finalizedRoot=0xb851a4de... justifiedEpoch=9 justifiedRoot=0xa9d281a0... parentHash=0x97465b93778e parentRoot=0x1585e510... sinceSlotStartTime=92.777546ms slot=323 slotInEpoch=3 version=gloas
[2026-03-04 15:36:35.09]  INFO blockchain: Finished applying state transition attestations=1 blobKzgCommitmentCount=0 builderIndex=18446744073709551615 parentHash=0x97465b93778e payloadHash=0x03ec7c6b614f slot=323 syncBitsCount=490
[2026-03-04 15:36:35.10]  INFO rpc/validator: Publishing signed execution payload envelope beaconBlockRoot=0x9ca431b17de2a50d builderIndex=18446744073709551615 slot=323
[2026-03-04 15:36:35.11]  INFO blockchain: Processed execution payload envelope blockHash=0x03ec7c6b614f blockRoot=0x9ca431b17de2 parentHash=0x97465b93778e slot=323````
[2026-03-04T15:37:52.030000+00:00] qu0b: did you mean <@641548687813902336> ?
[2026-03-04T15:37:59.580000+00:00] potuz: And LH+Prysm are in the same branch [2026-03-04 15:37:35.03] DEBUG sync: Received block blockSlot=328 graffiti=lighthouse-geth-3 proposerIndex=570 sinceSlotStartTime=29.109539ms validationTime=1.582457ms
[2026-03-04 15:37:35.04]  INFO blockchain: Synced new block block=0xa69fb558... blockHash=0xed8ef82438cf builderIndex=18446744073709551615 chainServiceProcessedTime=14.460951ms epoch=10 finalizedEpoch=8 finalizedRoot=0xb851a4de... justifiedEpoch=9 justifiedRoot=0xa9d281a0... parentHash=0xba02336f089a parentRoot=0x6fe82282... sinceSlotStartTime=45.41715ms slot=328 slotInEpoch=8 version=gloas
[2026-03-04 15:37:35.04]  INFO blockchain: Finished applying state transition attestations=1 blobKzgCommitmentCount=0 builderIndex=18446744073709551615 parentHash=0xba02336f089a payloadHash=0xed8ef82438cf slot=328 syncBitsCount=320
[2026-03-04 15:37:35.04] DEBUG sync: Verified and saved pending attestations to pool aggregateAttAndProofCount=0 attCount=3 blockRoot=0xa69fb558798a7e1878fb0179fa27e2821832145186eec437f5babe37e741700b durationAggregateAttAndProof=581ns durationAtts=2.28556ms durationTotal=2.286141ms totalCount=3 uniqueAggregateAttAndProofCount=0
[2026-03-04 15:37:35.04] DEBUG sync: Requesting blocks by root count=2 peer=16Uiu2HAkwrQaRVHAWah1Csf33wbUKRRghieAyBJkKJAZ2JYrHaXR roots=[0xcf0daabb9d66e0c94d7844da897989f0e449fb0c36fafbb1a003f1ebf8ed8320 0x778c0f874677a9d6ed4caa7bbced8ee18036695e1c22559ccb9ad6b6751a14fd]
[2026-03-04 15:37:35.05] DEBUG sync: Fetched data column sidecars from storage finalMissingRootCount=0 initialMissingRootCount=0
[2026-03-04 15:37:35.06]  INFO blockchain: Processed execution payload envelope blockHash=0xed8ef82438cf blockRoot=0xa69fb558798a parentHash=0xba02336f089a slot=328
[2026-03-04T15:38:12.719000+00:00] potuz: thanks indeed! you guys need to find a new avatar 🙂
[2026-03-04T15:39:07.925000+00:00] stefanbratanov: not sure, I fixed few things after the devnet has started, it's possible we were producing garbage
[2026-03-04T15:43:07.508000+00:00] barnabasbusa: <@641548687813902336> this is exactly why I prefer if we have a constant naming scheme for branches
[2026-03-04T15:43:11.888000+00:00] barnabasbusa: now we could just target epbs-devnet-0
[2026-03-04T15:43:15.343000+00:00] barnabasbusa: instead of focusing on master
[2026-03-04T15:43:29.487000+00:00] barnabasbusa: we can do quicker iterations without needing to worry whether or not it will break master
[2026-03-04T15:43:47.227000+00:00] barnabasbusa: now we have a fork of yours with a random branch name thats gonna be impossible to know by heart
[2026-03-04T15:43:59.476000+00:00] barnabasbusa: any way we could move your branch to epbs-devnet-0 in the upstream repo?
[2026-03-04T15:44:17.225000+00:00] barnabasbusa: then I can also enable auto build for this branch and its gonna be easy to keep track of
[2026-03-04T15:44:19.335000+00:00] potuz: Is Teku and Nimbus in the devnet? I have not gotten any blocks from them in a while and the chain has stabilized, LH+PRysm+Lodestar seem to be in the same branch as far as I can tell
[2026-03-04T15:44:31.408000+00:00] barnabasbusa: yes, only few valis tho
[2026-03-04T15:44:49.357000+00:00] barnabasbusa: looks like nimbus is stuck on slot 160
[2026-03-04T15:45:38.168000+00:00] potuz: I only found a few instances on Prysms' blocks being reorged when I actually think PRysm was *correct* building on empty, so I think your forkchoice impl must be wrong <@358120958726373381> <@1035747431461167125>
[2026-03-04T15:46:21.421000+00:00] potuz: Prysm gets reorged only because Lodestar and LH vote against it. I think it's too early to start diagnosing these issues though on this devnet, so I'll wait a few days to focus on forkchoice
[2026-03-04T15:46:35.639000+00:00] potuz: seems we can converge on the happy case and can handle minor splits
[2026-03-04T15:47:08.143000+00:00] potuz: ```
[2026-03-04 15:46:11.18]  INFO blockchain: Synced new block block=0x0d93ab7c... blockHash=0xb648baa2ee9d builderIndex=18446744073709551615 chainServiceProcessedTime=13.936481ms epoch=11 finalizedEpoch=9 finalizedRoot=0xa9d281a0... justifiedEpoch=10 justifiedRoot=0xb953af1d... parentHash=0x4b622d75c931 parentRoot=0x6eeb8131... sinceSlotStartTime=184.062711ms slot=371 slotInEpoch=19 version=gloas
```
[2026-03-04T15:51:31.309000+00:00] potuz: ok, now I'm seeing reorgs again
[2026-03-04T15:52:55.440000+00:00] barnabasbusa: [dora is live](https://dora.epbs-devnet-0.ethpandaops.io/)
[2026-03-04T15:52:56.553000+00:00] potuz: yeah now we seem to be split
```

</details>
