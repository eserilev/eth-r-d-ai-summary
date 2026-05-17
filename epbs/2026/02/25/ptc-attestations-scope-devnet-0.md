# PTC attestations scope for devnet 0

**Channel:** epbs | **Date:** 2026-02-25

## Summary

The discussion centers on whether PTC (Payload Timeliness Committee) attestations should be included in devnet 0's scope. Stefan confirms they will not be included, but Potuz raises concerns about client implementation compatibility.

Potuz worries that without PTC attestations, he'll need to modify the fork choice logic to return "full" for the head when there are no PTC votes. He notes this might be acceptable since proposer calls return "full" when the current block is still the head anyway, but expresses concern about potential inconsistencies between clients.

The main open issue is ensuring all clients handle the absence of PTC attestations consistently, particularly regarding the fork choice tiebreaker mechanism - some clients may implement the full tiebreaker while others don't, potentially causing a split in behavior across the network.

## Participants

- barnabasbusa
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-25T14:02:30.525000+00:00] barnabasbusa: do we do ptc attestations in devnet 0 scope?
[2026-02-25T14:04:46.779000+00:00] stefanbratanov: No
[2026-02-25T14:07:04.650000+00:00] potuz: Are clients going to be able to process them? I am worried of not doing PTC attestations cause I'll have to hack forkchoice to return full for head if there are no PTC votes
[2026-02-25T14:07:53.904000+00:00] potuz: I think it's fine anyway cause the proposer calls when still head returns full anyway
[2026-02-25T14:08:25.706000+00:00] potuz: But I worry about a split between clients that have the full tiebreaker and those that don't
```

</details>
