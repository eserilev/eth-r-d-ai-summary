# Bug in serving envelopes by range

**Channel:** epbs | **Date:** 2026-03-11

## Summary

0xunclebill reported a bug in the envelope serving mechanism where non-canonical envelopes may be served when requesting envelopes by range. The issue occurs because the system doesn't verify that served envelopes match the current chain view when serving payloads.

The proposed solution involves loading blocks during payload serving to validate envelope canonicity, though this check can be skipped for finalized data since non-canonical data should already be pruned. For non-finalized data, the validation check is necessary to ensure correctness.

On devnet-0 specifically, the validation check is required for both finalized and non-finalized data because pruning is not currently implemented, making it possible for non-canonical finalized data to still exist in the system.

## Participants

- 0xunclebill

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T04:16:27.208000+00:00] 0xunclebill: just fyi we do have a bug in serving envelopes by range where we might serve non canonical envelopes. we need to load the blocks when serving payloads to check that the envelopes being served match our view of the chain. in the case of serving finalized data, we can skip this check because non canonical data will have already been pruned. but we need this check when serving non-finalized data. on devnet-0 we need this check for finalized and non finalized data because were not doing any pruning atm
```

</details>
