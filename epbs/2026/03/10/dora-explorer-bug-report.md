# Dora Explorer Payload Orphan Status Bug Discussion

**Channel:** epbs | **Date:** 2026-03-10

## Summary

**potuz** discovered a bug in the Dora Explorer where a payload at slot 38752 shows inconsistent orphan status between different views. The payload appeared green (non-orphaned) in one view, but according to potuz, it should have been marked as orphaned since Prysm's branch correctly orphaned it while Lodestar's branch was trying to build on top of it. Eventually Prysm's chain won, meaning the payload should indeed be orphaned.

**pk910** explained that Dora determines orphan status by checking if the next canonical block's parent execution block hash matches the current execution payload hash, and this status can change as Dora's view of the canonical chain updates. They identified that while the details page shows the correct status, the list view displays incorrect information, indicating a discrepancy in the logic between these two views.

**pk910** committed to investigating why the two views show different results despite supposedly using the same logic, and potuz confirmed that the display had updated to show different information by the end of the discussion.

## Participants

- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-10T00:00:45.924000+00:00] potuz: <@808969530608451584> I don' t understand Dora, look at this payload https://dora.epbs-devnet-0.ethpandaops.io/slot/38752 in that pane
[2026-03-10T00:00:50.769000+00:00] potuz: and then look at this
[2026-03-10T00:01:31.412000+00:00] potuz: That payload appears green
[2026-03-10T00:02:11.794000+00:00] pk910: yea,  I see.   the payload orphaned status doesn't match
[2026-03-10T00:02:26.718000+00:00] potuz: on the Prysm branch it was actually orphaned
[2026-03-10T00:02:33.740000+00:00] potuz: and it should have been orphaned
[2026-03-10T00:02:45.824000+00:00] potuz: in Lodestar' s branch it was trying to build on top of full
[2026-03-10T00:02:53.615000+00:00] potuz: and not orphan it
[2026-03-10T00:03:25.162000+00:00] potuz: Eventually Prysm wins so it should be orphaned
[2026-03-10T00:04:40.887000+00:00] pk910: dora gets the next canonical block that builds on top of the current block and checks if the parent execution block hash matches the current execution payload hash.
that might change if dora sees another chain as canonical.

so yea, the details page gets it right,  but the list view is wrong
[2026-03-10T00:05:21.817000+00:00] potuz: ah so the details page is the one that gets actualized when the chain changes the canonical view?
[2026-03-10T00:05:30.254000+00:00] potuz: I should trust more the details page in general?
[2026-03-10T00:05:40.906000+00:00] potuz: for older blocks at least right?
[2026-03-10T00:05:56.233000+00:00] pk910: both change.  the logic should be equal 😄   let me check 🙂
[2026-03-10T00:06:27.407000+00:00] potuz: ah yeah it shows differently now
```

</details>
