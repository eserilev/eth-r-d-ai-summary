# Prysm Reorging Issues and Chain Behavior

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around Prysm experiencing constant reorging issues during testing against a 66% malicious majority scenario. Potuz observed that Prysm is struggling with chain weight calculations, showing debug logs where the head block has significantly higher weight (4.6B) compared to received blocks (760M), with all validator votes going to empty blocks. The reorging pattern is causing Prysm to miss payload cache and subsequently fail block proposals, particularly when blocks follow empty ones.

Despite these issues, Prysm eventually managed to justify epoch 115 and sync new blocks, suggesting the client can recover from the adverse conditions. Potuz noted that while this represents buggy behavior that should be addressed, the testing scenario is valuable for identifying these edge cases in handling malicious majority attacks.

The team decided to continue with the current testing setup since it's effectively surfacing bugs that need to be fixed, even though Prysm's performance is degraded under these conditions.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T02:51:26.768000+00:00] potuz: As I expected, Prysm is reorging constantly
```[2026-03-05 02:50:59.05] DEBUG blockchain: Head block is not the received block headEmptyWeight=1888000000000 headFullWeight=1336000000000 headRoot=0x2879719a82baac70b376055659d4fae99dfe080e22894bb9626b3a588056fa04 headWeight=4632000000000 receivedRoot=0xf27af98fd3231fb5fd27e7b87d637751ce9f1d1a03862cd5ec7c166128df15d0 receivedWeight=760000000000````
this is not on us
[2026-03-05T02:51:40.855000+00:00] potuz: all votes are on empty
[2026-03-05T02:54:19.829000+00:00] potuz: I think Prysm will only be able to propose well after there are full blocks cause whenever there's a block after an empty one it finds itself reorged and misses the payload cache
[2026-03-05T02:54:55.938000+00:00] potuz: probably better to keep things as is TBH, I'm finding a bunch of bugs
[2026-03-05T02:55:14.962000+00:00] potuz: this scenario is being against a 66% malicious majority
[2026-03-05T02:55:29.637000+00:00] potuz: and we should be able to handle it correctly, but we're missing proposals after the reorg
[2026-03-05T02:56:26.107000+00:00] potuz: We'll justify this epoch now
[2026-03-05T02:57:01.205000+00:00] potuz: ```[2026-03-04 23:56:11.10]  INFO blockchain: Synced new block block=0xaf5d4fc2... blockHash=0x681f5ec77d62 builderIndex=18446744073709551615 chainServiceProcessedTime=18.174542ms epoch=116 finalizedEpoch=10 finalizedRoot=0xb953af1d... justifiedEpoch=115 justifiedRoot=0x0a6ee7ed... parentHash=0x1e223cc3ffde parentRoot=0xea90051b... sinceSlotStartTime=109.822ms slot=3721 slotInEpoch=9 version=gloas````
[2026-03-05T02:57:13.043000+00:00] potuz: `justifiedEpoch=115 ``
[2026-03-05T02:57:25.686000+00:00] 0xunclebill: hell yeah
```

</details>
