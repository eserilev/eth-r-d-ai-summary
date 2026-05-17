# Attestation deadline timing optimization discussion

**Channel:** epbs | **Date:** 2026-03-03

## Summary

nero_eth initiated a discussion about optimizing the attestation deadline timing, which is currently set at ¼ of the slot. They proposed moving it to ⅙ of the slot (~1667 basis points), arguing this would provide sufficient time for small blocks to propagate while leaving more time for downstream activities.

potuz agreed with the proposal, stating they could "squeeze this a LOT" more than the current timing allows. There was some discussion about the testing timeline, with potuz initially suggesting it would be "months" before they could test the change, but barnabasbusa corrected this to "weeks." The conversation suggests consensus around the optimization being feasible, though the exact testing timeline remains to be clarified.

## Participants

- barnabasbusa
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-03T17:27:07.628000+00:00] nero_eth: What is the latest on the attestation dealine?
I think it's at ¼ of the slot right now and I'm quite convinced that moving it to ⅙ (~1667 BPS) would be fine - enough time for the small block to propagate, leaving more time for downstream activities.
[2026-03-03T18:30:36.422000+00:00] potuz: Months until we can test that, but I agree with you, we can squeeze this a LOT
[2026-03-03T19:32:36.450000+00:00] barnabasbusa: weeks *
[2026-03-03T19:53:15.717000+00:00] potuz: Yes! Four times as many
```

</details>
