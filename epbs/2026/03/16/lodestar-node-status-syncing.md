# Lodestar Node Status and Syncing

**Channel:** epbs | **Date:** 2026-03-16

## Summary

This discussion concerned the status of a Lodestar Ethereum client node. Potuz initially reported that "Lodestar 2" appeared to be down, prompting investigation into the node's availability.

Barnabasbusa clarified that the node wasn't actually down but was instead performing a full sync from genesis, meaning it was rebuilding the complete blockchain history from the very first block. Potuz expressed approval of this approach, noting that it was beneficial to preserve the complete historical record rather than using a faster sync method that might skip historical data.

No immediate action items or unresolved issues were identified, as the syncing process appears to be intentional and proceeding as expected.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T19:18:40.666000+00:00] potuz: Lodestar 2 seems to be down <@586161934425128960>
[2026-03-16T20:36:28.652000+00:00] barnabasbusa: it’s syncing from genesis
[2026-03-16T20:41:12.720000+00:00] potuz: ah that's good not to lose history
```

</details>
