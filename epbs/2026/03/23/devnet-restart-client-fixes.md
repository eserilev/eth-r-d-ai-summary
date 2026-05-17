# Network Recovery and Client Implementation Fixes

**Channel:** epbs | **Date:** 2026-03-23

## Summary

The discussion centers around efforts to recover a testnet that appears to be experiencing issues, with barnabasbusa coordinating recovery efforts for what seems to be an ePBS (enshrined Proposer-Builder Separation) implementation. The conversation reveals that while fixes exist for withdrawals functionality, there are ongoing synchronization problems preventing proper network operation.

The main technical blocker identified is checkpoint sync functionality, which 0xunclebill indicates hasn't seen much progress but is targeted for advancement within the week. Barnabasbusa proposes moving validator keys around to restore finalization and suggests using Lodestar for checkpoint sync with a 2-3 day old checkpoint, which terencechain confirms should work as long as a fresh database is used.

The recovery effort is contingent on having working client implementations, with barnabasbusa expressing reluctance to proceed without confirmation that the necessary fixes are in place. The timeline for resolving the checkpoint sync issues and getting Lighthouse (lh) back operational remains the key dependency for network recovery.

## Participants

- 0xunclebill
- barnabasbusa
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-23T06:26:00.716000+00:00] barnabasbusa: <@363800010518822915> can we get this into the epbs branch ?
[2026-03-23T06:26:10.555000+00:00] barnabasbusa: gonna try to recover the network
[2026-03-23T12:39:05.071000+00:00] barnabasbusa: <@358120958726373381> do you also have a fix for the withdrawals?
[2026-03-23T12:39:20.258000+00:00] barnabasbusa: I'm happy to try to get the network back in working order if I know that we have client implementations that could work
[2026-03-23T12:39:28.607000+00:00] barnabasbusa: otherwise its just gonna be a waste of effort
[2026-03-23T14:00:29.332000+00:00] 0xunclebill: yeah we have a fix but we can’t sync rn
[2026-03-23T14:00:57.307000+00:00] barnabasbusa: ah right, how far out is that?
[2026-03-23T14:01:10.234000+00:00] barnabasbusa: I could move around some validator keys to get us back to finalization
[2026-03-23T14:01:21.331000+00:00] barnabasbusa: so if checkpoint sync could be fixed, then we could get lh back in the game
[2026-03-23T14:02:04.825000+00:00] 0xunclebill: We haven’t made much progress on checkpoint sync hopefully we can push it forward this week
[2026-03-23T20:16:25.213000+00:00] barnabasbusa: should i be able to checkpoint sync from lodestar?
[2026-03-23T20:16:40.569000+00:00] barnabasbusa: i know it’s a 2-3d old checkpoint so might take a while to get to head
[2026-03-23T20:16:55.524000+00:00] terencechain: its fine as long as it's a fresh DB
```

</details>
