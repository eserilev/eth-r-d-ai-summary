# Spec tests for onboard_builders_from_pending_deposits in alpha.2

**Channel:** epbs | **Date:** 2026-02-18

## Summary

terencechain inquired about the availability of spec tests for `onboard_builders_from_pending_deposits` in alpha.2. jtraglia confirmed that both the spec tests exist and are included in alpha.2, providing specific examples including a test case at `fork_builder_deposit_uses_deposit_slot_epoch` and directing to the complete test suite in the `test_gloas_fork_onboard_builders.py` file.

The discussion was resolved quickly with jtraglia providing the requested test references from the consensus-specs repository, specifically for the Gloas fork implementation. terencechain expressed satisfaction with the provided information, indicating the question was fully answered.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-18T00:10:18.917000+00:00] terencechain: Hello, is there spec test for `onboard_builders_from_pending_deposits`, and is that already in `alpha.2`?
[2026-02-18T01:05:05.268000+00:00] jtraglia: Yes & yes. I can find it in a few minutes.
[2026-02-18T01:07:49.421000+00:00] jtraglia: Eg this one: https://ethspec.tools/#tests/v1.7.0-alpha.2/mainnet/gloas/fork/fork/pyspec_tests/fork_builder_deposit_uses_deposit_slot_epoch
[2026-02-18T01:07:53.777000+00:00] jtraglia: And everything in this file: https://github.com/ethereum/consensus-specs/blob/master/tests/core/pyspec/eth_consensus_specs/test/gloas/fork/test_gloas_fork_onboard_builders.py
[2026-02-18T01:11:39.334000+00:00] terencechain: Thanks! That's really helpful
```

</details>
