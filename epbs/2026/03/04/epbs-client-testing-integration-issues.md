# Kurtosis testing issues with Teku and Nimbus

**Channel:** epbs | **Date:** 2026-03-04

## Summary

During Kurtosis testing of the latest Ethereum client images, multiple consensus layer clients experienced synchronization issues. Teku built on top of a slot that was 2 epochs old and completely forked off the main chain, which subsequently caused Nimbus to stop syncing. Later, Lodestar also orphaned a block due to an error where the "Payload Timeliness Committee is not available for slot=47" when trying to process a block at slot 48.

The root cause appears to be related to Payload Timeliness Committee (PTC) attestations. Potuz identified that Teku likely packed PTC attestations in block 48, which Lodestar cannot properly validate because it lacks access to the PTC data for slot 47. This suggests a potential bug in Lodestar's state cache handling for PTC validation. Prysm also caused reorgs of both Lighthouse and Nimbus at slot 55, though the exact cause remains unclear.

As immediate mitigation, barnabasbusa decided to reduce the validator count and run single nodes for Teku and Nimbus during testing. The team identified that the problematic Teku image was "ethpandaops/teku:StefanBratanov-devnet-0-changes" and stefanbratanov offered to disable PTC packing in that branch while fixing other issues. The Lodestar team (nflaig) acknowledged they need to implement PTC duties properly to test this code path, as they don't currently pack PTC attestations themselves.

## Participants

- barnabasbusa
- nflaig
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T13:18:39.776000+00:00] barnabasbusa: <@641548687813902336>
[2026-03-04T13:18:47.541000+00:00] barnabasbusa: teku built on top of a slot thats 2 epochs old
[2026-03-04T13:18:58.067000+00:00] barnabasbusa: and completely forked off
[2026-03-04T13:20:43.269000+00:00] barnabasbusa: following that, nimbus also stopped syncing
[2026-03-04T13:21:01.260000+00:00] barnabasbusa: I think I'll actually just do 1 / 1 node for teku and nimbus with less validators
[2026-03-04T13:21:02.207000+00:00] barnabasbusa: sorry
[2026-03-04T13:22:26.406000+00:00] potuz: Ah this is Kurtosis? I thought it was devnet and we were already cooked
[2026-03-04T13:22:43.737000+00:00] barnabasbusa: No, I told you i'm gonna validate latest images 😄
[2026-03-04T13:23:10.143000+00:00] potuz: if you delay a little more we'll have syncing ready so I don't mind if it gets cooked 🙂
[2026-03-04T13:23:31.262000+00:00] barnabasbusa: once teku proves to be stable for a few hours I can add more validators
[2026-03-04T13:23:43.337000+00:00] stefanbratanov: Gloas was upgraded at slot 32?
[2026-03-04T13:23:46.800000+00:00] barnabasbusa: But if we wanna do genesis now we gotta keep it on the low end
[2026-03-04T13:23:54.097000+00:00] barnabasbusa: slot 8
[2026-03-04T13:24:08.391000+00:00] barnabasbusa: minimal preset
[2026-03-04T13:24:23.573000+00:00] stefanbratanov: Do you see failed to produce attestation errors
[2026-03-04T13:25:21.655000+00:00] barnabasbusa: sorry I just restarted the encalve to check the new prysm image too
[2026-03-04T13:25:29.515000+00:00] barnabasbusa: will see if I can repro it
[2026-03-04T13:31:39.958000+00:00] barnabasbusa: now lodestar orphaned a block
[2026-03-04T13:31:41.985000+00:00] barnabasbusa: ```
Mar-04 13:31:24.362[sync]            debug: Error processing block from unknown parent sync slot=48, root=0xee3a94d4ced757690877848557db4b5b1eaeb1b6c3a4c756d0c1cbece95d0008, code=BLOCK_ERROR_BEACON_CHAIN_ERROR, error=Payload Timeliness Committee is not available for slot=47
Error: Payload Timeliness Committee is not available for slot=47
    at EpochCache.getPayloadTimelinessCommittee (file:///usr/app/packages/state-transition/src/cache/epochCache.ts:1033:13)
    at EpochCache.getIndexedPayloadAttestation (file:///usr/app/packages/state-transition/src/cache/epochCache.ts:1043:45)
    at processPayloadAttestation (file:///usr/app/packages/state-transition/src/block/processPayloadAttestation.ts:20:52)
    at processOperations (file:///usr/app/packages/state-transition/src/block/processOperations.ts:92:7)
    at processBlock (file:///usr/app/packages/state-transition/src/block/index.ts:85:3)
    at stateTransition (file:///usr/app/packages/state-transition/src/stateTransition.ts:125:3)
    at verifyBlocksStateTransitionOnly (file:///usr/app/packages/beacon-node/src/chain/blocks/verifyBlocksStateTransitionOnly.ts:48:23)
    at BeaconChain.verifyBlocksInEpoch (file:///usr/app/packages/beacon-node/src/chain/blocks/verifyBlock.ts:184:7)
    at BeaconChain.processBlocks (file:///usr/app/packages/beacon-node/src/chain/blocks/index.ts:77:7)
    at JobItemQueue.runJob (file:///usr/app/packages/beacon-node/src/util/queue/itemQueue.ts:149:22)
```
[2026-03-04T13:32:11.888000+00:00] barnabasbusa: 
[2026-03-04T13:32:19.344000+00:00] barnabasbusa: https://klipy.com/gifs/forky-toy-story-4
[2026-03-04T13:32:44.980000+00:00] barnabasbusa: 
[2026-03-04T13:32:48.328000+00:00] barnabasbusa: LGTM 😂
[2026-03-04T13:40:00.762000+00:00] potuz: lol, how does Dora choose "canonical"
[2026-03-04T13:41:05.351000+00:00] potuz: Anyway my guess looking at that fork is that Teku packed PTC attestations on slot 48 and Lodestar cannot handle this
[2026-03-04T13:41:12.461000+00:00] potuz: can you confirm <@586161934425128960> ?
[2026-03-04T13:46:25.201000+00:00] potuz: If you have logs I would like to understand why Prysm reorgs on 55 both LH and Nimbus, that will explain if they are both building on a bad state or if Prysm is doing it incorrectly. Since both LH and Nimbus are reorging themselves, no one knows 🙂
[2026-03-04T13:47:58.633000+00:00] barnabasbusa: wonder if its a minimal spec mismatch maybe
[2026-03-04T13:48:11.223000+00:00] barnabasbusa: would have shown earlier then
[2026-03-04T13:49:43.279000+00:00] potuz: nah the error from Lodestar clearly states that they do not have access to the PTC of 47, I think this means that they can't validate Teku's block 48 (which I am guessing included PTC attestations, if you have the block you can check) and that means that Lodestar will be stuck at 47
[2026-03-04T13:50:03.309000+00:00] barnabasbusa: right
[2026-03-04T13:58:47.068000+00:00] nflaig: in theory I thought we should be able to handle it but this is the first time the code path was executed since we don't pack ptc attestations ourselves right now
[2026-03-04T13:59:24.752000+00:00] potuz: Is that stack trace compatible with my understanding? you probably don't have the state to get the PTC?
[2026-03-04T13:59:56.935000+00:00] nflaig: we should have it though, might be a bug in our state cache
[2026-03-04T14:00:07.393000+00:00] nflaig: need to implement ptc duties first so I can properly test this
[2026-03-04T14:01:13.815000+00:00] potuz: I am not sure if we pack on that branch, I believe we don't, and I think Teku has a flag to not include them
[2026-03-04T14:03:53.059000+00:00] nflaig: what teku image caused this, wanna look into this first
[2026-03-04T14:20:41.155000+00:00] barnabasbusa: ```
- cl_type: teku
  cl_image: ethpandaops/teku:StefanBratanov-devnet-0-changes
```
[2026-03-04T14:21:11+00:00] barnabasbusa: <@641548687813902336> what is this flag?
[2026-03-04T14:21:14.052000+00:00] barnabasbusa: I'd like to enable it
[2026-03-04T14:28:34.556000+00:00] stefanbratanov: We don't have a flag,but can disable it in that PR while I fix other things
```

</details>
