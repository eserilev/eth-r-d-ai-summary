# Syncing to Finalized Epoch and Quick Finalization

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around resolving finalization issues on what appears to be a test chain. barnabasbusa initially suggests syncing to a finalized epoch and proposes redirecting Teku/Nimbus validators to Lighthouse (LH) to achieve quick finalization. Later, they offer an alternative approach of moving some Prysm validators to point to Lighthouse to restore finalization.

potuz responds that moving validators isn't necessary, suggesting instead that they can fix an existing sync bug to enable syncing to finalization directly. They note that since it's an empty chain, the simpler approach of fixing the sync issue would be preferable to validator reshuffling.

The conversation leaves an open action item, with potuz indicating they could try the fix later if Terence hasn't already resolved the sync bug.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T18:26:01.981000+00:00] barnabasbusa: can we sync to a finalized epoch?
[2026-03-04T18:26:19.491000+00:00] barnabasbusa: we could point the teku nimbus vali to lh
[2026-03-04T18:26:24.707000+00:00] barnabasbusa: and finalize quickly
[2026-03-04T22:02:18.251000+00:00] barnabasbusa: do we wanna finalize?
[2026-03-04T22:02:27.377000+00:00] barnabasbusa: or can we leave the chain in its current state?
[2026-03-04T22:02:50.453000+00:00] barnabasbusa: i could try to shuffle a few prysm validators to point to lh and get us to finalize again
[2026-03-04T22:04:25.564000+00:00] potuz: nah, we can just fix the sync bug and just sync to finalization
[2026-03-04T22:04:30.392000+00:00] potuz: no need to move vals
[2026-03-04T22:04:37.349000+00:00] potuz: it's an empty chaing
[2026-03-04T22:04:46.855000+00:00] potuz: can try later if Terence hasn't fixed it
```

</details>
