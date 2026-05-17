# PTC Spec Test Coverage and Invariants

**Channel:** epbs | **Date:** 2026-01-28

## Summary

Terencechain reported that their optimized implementation of `get_ptc` (presumably a function related to Ethereum's consensus mechanism) contained multiple consensus bugs that were not detected by the existing specification tests. This highlights gaps in the current test coverage that allowed incorrect implementations to pass validation.

To address these testing gaps, they proposed updating the spec tests to cover two specific invariants: ensuring there is more than one `committee_per_slot`, and verifying that sampling terminates only after the iterator `i` exceeds the `active_validator_count`. These additions would help catch similar consensus bugs in future implementations and improve the robustness of the test suite.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-28T00:33:16.299000+00:00] terencechain: My optimized version of `get_ptc` had multiple consensus bugs that spec tests did not catch. I think it is worth updating the spec tests to cover the following invariants
* There is more than one `committee_per_slot`
* Sampling ended after `i` is greater than the `active_validator_count`
```

</details>
