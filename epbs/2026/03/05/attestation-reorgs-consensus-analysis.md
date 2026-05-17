# Attestation reorgs and consensus analysis

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz identified a significant issue with Prysm client synchronization, highlighting that Prysm will experience constant reorgs even when synced to head. The problem is demonstrated through block 3264 on the epbs-devnet-0, where all attestations from the committee in slot 3263 (which was missed) voted for the block from slot 3262 with index=0.

This creates a structural problem where even if Prysm were to propose the next block, it would build on an empty chain rather than the full chain, indicating a fundamental issue with how Prysm handles fork choice in scenarios involving missed slots and attestation patterns. The discussion appears to be ongoing analysis of consensus behavior in edge cases.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T01:29:36.902000+00:00] potuz: So I really want to see us synced to head cause Prysm will be reorged constantly even then. Look at this block https://dora.epbs-devnet-0.ethpandaops.io/slot/3264, all attestations included from the committee in 3263 (which was missed) voted for the block of 3262 but with index=0
[2026-03-05T01:29:49.440000+00:00] potuz: even if Prysm were to propose the next block, it would do it over empty not full
```

</details>
