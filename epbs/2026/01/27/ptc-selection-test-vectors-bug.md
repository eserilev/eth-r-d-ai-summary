# PTC selection test vectors bug

**Channel:** epbs | **Date:** 2026-01-27

## Summary

potuz reported a bug in PTC (Payload Timeliness Committee) selection that went undetected by existing test vectors. The bug involved incorrectly looping through committees when selecting PTC members, but all current tests pass because they only select PTC members from the first committee.

The issue highlights a gap in test coverage, as the existing test vectors don't adequately cover realistic PTC selection scenarios across multiple committees. potuz suggests that mainnet vectors or more realistic PTC selection test vectors are needed to catch such bugs in the future.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T19:36:07.847000+00:00] potuz: FWIW: we need mainnet vectors or more reallistic PTC selection testvectors, we had a bug selecting PTC members from committees looping through them and passed tests anyway, this is beause all tests must select the PTC from the first committee only
```

</details>
