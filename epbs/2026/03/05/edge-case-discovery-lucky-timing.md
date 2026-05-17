# Edge Case Discovery and Lucky Timing

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz identified an edge case that barnabasbusa encountered during sync from an existing database. The issue occurred due to particularly lucky (or unlucky) timing - barnabasbusa started their pod precisely when there were 3 consecutive missed slots at the justified checkpoint, specifically at slot 4576 on the epbs-devnet-0 testnet.

Barnabasbusa acknowledged their "lucky" timing in hitting this rare edge case, noting that they keep encountering such unusual scenarios. The discussion appears to be documenting a specific synchronization bug that manifests under very particular timing conditions involving missed slots at critical checkpoints.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T11:47:08.041000+00:00] potuz: <@412614104222531604> found the edge case on sync from existing dbase you hit, you started the pod precisely when there were 3 missed slots in a row at the justified checkpoint https://dora.epbs-devnet-0.ethpandaops.io/slot/4576
[2026-03-05T11:48:23.967000+00:00] barnabasbusa: I'm a lucky man
[2026-03-05T11:48:29.055000+00:00] barnabasbusa: I keep hearing this
```

</details>
