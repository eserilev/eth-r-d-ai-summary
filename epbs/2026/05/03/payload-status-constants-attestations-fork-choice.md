# Payload status badge for same head slot attestations

**Channel:** epbs | **Date:** 2026-05-03

## Summary

In this brief discussion, tbenr suggests modifying the payload status badge behavior when attestations target the same head slot. They propose that in such cases, the payload status should be changed to either PENDING or UNSPECIFIED rather than using the current status designation.

This appears to be a targeted recommendation for handling a specific edge case in attestation processing, though the discussion lacks additional context about the current behavior, rationale for the change, or responses from other participants. The suggestion remains unaddressed and would likely require further discussion to evaluate its technical merits and implementation details.

## Participants

- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-03T00:15:36.250000+00:00] tbenr: <@808969530608451584>  i believe in case the attestations are for the same head slot, it is better to change the payload status badge to PENDING or UNSPECIFIED
```

</details>
