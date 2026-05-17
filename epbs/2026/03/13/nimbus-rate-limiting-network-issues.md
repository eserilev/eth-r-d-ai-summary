# Nimbus Rate Limiting and Network Issues

**Channel:** epbs | **Date:** 2026-03-13

## Summary

The discussion centers around network synchronization issues between Nimbus and Prysm clients on the EPBS devnet. 0xshum reported that after Nimbus was rate limited from envelope byrange/byroot requests, Prysm appeared to stop functioning properly. Potuz acknowledged that Prysm was likely "spammed" with requests and stopped following the chain, but clarified this would be Prysm's fault rather than Nimbus's, as Nimbus was simply being "too eager to sync up."

Potuz identified several technical issues affecting their node's performance, including peering problems, extensive downscoring of peers, and numerous errors in the logs. The node was only synced to block 1444 out of 65031 due to continuously "kicking people out." Potuz also noted a dilemma with the `epbs-devnet-0` update: using the newest sync changes would allow immediate checkpoint syncing but prevent the node from serving blocks/payloads to others, while syncing from genesis would enable serving other nodes but likely cause significant downscoring due to peers serving incorrect non-canonical payloads.

The discussion ends with Potuz asking whether to proceed with pushing the update and checkpoint syncing one of the nodes despite these trade-offs, leaving this as an open decision point.

## Participants

- 0xshum
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-13T14:27:26.828000+00:00] 0xshum: After Nimbus is being rate limited from envelope byrange/byroot requests, it seems that Prysm would stop working for some reasons?
[2026-03-13T14:53:54.590000+00:00] potuz: oh interesting, we were spammed and stopped following the chain?
[2026-03-13T15:01:01.903000+00:00] 0xshum: Probably Nimbus is too eager to sync up so there were too many requests. Not intended to spam I believe.
[2026-03-13T15:01:23.039000+00:00] potuz: nah if we were spammed it's our fault not Nimbus 🙂
[2026-03-13T15:01:35.996000+00:00] potuz: looking at logs, there's plenty of errors being shown
[2026-03-13T15:01:56.418000+00:00] potuz: we're also not peered so we may be downscoring a lot
[2026-03-13T15:18:11.271000+00:00] potuz: <@412614104222531604> here's the situation, if I update `epbs-devnet-0` with the newest sync changes, I'm pretty sure the node immediately checkpoint syncs fine. However, such a node won't be able to serve blocks/payloads to the others. If I sync it from genesis (so that they serve other nodes) then I think I may downscore a lot because peers are serving incorrectly some non-canonical payloads
[2026-03-13T15:18:38.489000+00:00] potuz: With a lot of errors I am now at 1444/65031
[2026-03-13T15:18:51.237000+00:00] potuz: I keep kicking people out 🙂
[2026-03-13T15:19:14.772000+00:00] potuz: want me to push anyway and checkpoin sync one of the nodes?
```

</details>
