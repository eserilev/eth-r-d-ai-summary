# Spec Test Gap Discovery

**Channel:** epbs | **Date:** 2026-01-09

## Summary

terencechain discovered a gap in the Ethereum specification tests during code review. They found that using `len(state.validators)` instead of the correct `active_validator_count(state)` function when selecting PTC (likely Payload Timeliness Committee) members still allowed all spec tests to pass, indicating that the current test suite doesn't adequately cover this validation scenario.

This represents a potentially significant testing gap, as the two approaches could yield different results in certain network states where the total validator count differs from the active validator count, but the existing tests don't catch this implementation error.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-09T21:52:59.578000+00:00] terencechain: Found a spec test gap through code review: I used `len(state.validators)` instead of `active_validator_count(state)` and it still passed spec tests where we were selecting PTC members
```

</details>
