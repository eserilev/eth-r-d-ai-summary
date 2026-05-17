# Technical Analysis of Prysm Reorg Issue

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz analyzed a Prysm reorg issue that occurred during restart, identifying that Prysm correctly reorged the payload of a Lighthouse block due to incorrect attestations. The reorg involved blocks at slot 4556 (Prysm) and a previous Lighthouse block, with Prysm's behavior being validated as correct since the attestations were problematic.

The analysis revealed a concerning difference in client behavior: while Prysm properly reorged the head view, Lighthouse and Lodestar did not reorg Prysm's block, suggesting these clients may accept any block that extends the chain as valid and vote on it without proper validation. Potuz noted that Prysm was able to produce the reorg block because it had sufficient time due to many missed slots beforehand, whereas it typically discovers reorg conditions at the last second and fails to propose.

The discussion leaves open questions about why Lighthouse and Lodestar failed to perform the expected reorg and whether their chain extension validation logic needs review. Potuz also noted that without the incorrect attestations, this validation issue might not have been discovered.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T12:35:12.499000+00:00] potuz: So just for info here <@358120958726373381> <@1035747431461167125> the issue Prysm had on restart is related to this block https://dora.epbs-devnet-0.ethpandaops.io/slot/4556 from Prysm that reorgs the payload of the latest Lighthouse block https://dora.epbs-devnet-0.ethpandaops.io/slot/1e084448fd4d52100acb0a209617a158a21452b948d9e9be5c0852ad335e21ac because the attestations are wrong. If the attestations weren't wrong I think we wouldn't have found this issue either 🙂
[2026-03-05T12:35:48.764000+00:00] potuz: So Prysm acts correctly here reorging your head view, what I don't understand is why LH and Lodestar did not reorg Prysm, it seems to me that you guys take anything that extends the chain as valid and vote on it
[2026-03-05T12:36:37.847000+00:00] potuz: Prysm could actually produce that block because it had time because of the many missed slots before. Otherwise it typically finds out the reorg at the very last second and fails to propose
```

</details>
