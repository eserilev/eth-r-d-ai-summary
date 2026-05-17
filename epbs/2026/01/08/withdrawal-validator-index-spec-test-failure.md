# Withdrawal Validator Index Update Spec Test Failure

**Channel:** epbs | **Date:** 2026-01-08

## Summary

nc1234 reported a spec test failure when implementing the new `update_next_withdrawal_validator_index()` function for Capella, where the calculated state root mismatches with v1.6.1 spec tests. The issue stems from a fundamental difference between the old and new specifications: the old spec doesn't track sweep count and assumes a full `MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP` was always processed, while the new spec increments by the actual sweep count performed.

The behavioral difference manifests when `len(state.validators) < 16384` on mainnet, causing `validators_limit = min(len(state.validators), MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP)` to be less than the maximum sweep size, and when `len(expected_withdrawals) < MAX_WITHDRAWALS_PER_PAYLOAD`. In these cases, the old spec would incorrectly advance the validator index by the full sweep amount rather than the actual number of validators processed.

nc1234 identified a specific failing test case (`capella/transition/core/pyspec_tests/transition_with_deposit_right_after_fork`) that demonstrates this incompatibility when running v1.6.1 tests against v1.7.0-alpha.0 spec with mainnet preset. jtraglia confirmed this is a known issue with an existing discussion thread, suggesting this problem has been identified by multiple implementers.

## Participants

- jtraglia
- nc1234

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-08T23:11:01.451000+00:00] nc1234: <@592004585506340885> I implemented the new `update_next_withdrawal_validator_index()` for capella and it seems to be failing 1.6.1 spec test because the calculated state root mismatches.
The old spec doesnt track the sweep count, so it updates `state.next_withdrawal_validator_index` less precise than the new spec.
```
    # Update the next validator index to start the next withdrawal sweep
    if len(expected_withdrawals) == MAX_WITHDRAWALS_PER_PAYLOAD:
        # Next sweep starts after the latest withdrawal's validator index
        next_validator_index = ValidatorIndex(
            (expected_withdrawals[-1].validator_index + 1) % len(state.validators)
        )
        state.next_withdrawal_validator_index = next_validator_index
    else:
        # Advance sweep by the max length of the sweep if there was not a full set of withdrawals
        next_index = state.next_withdrawal_validator_index + MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP
        next_validator_index = ValidatorIndex(next_index % len(state.validators))
        state.next_withdrawal_validator_index = next_validator_index
```
If we have reached `MAX_WITHDRAWALS_PER_PAYLOAD`, then we get validator index of last withdrawal + 1. Otherwise, we assume we have swept `MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP` and we just increment by that amount.
New spec we increment by actual sweep count. 

So the outcome between old spec and new spec will be different if we haven't swept `MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP` and `len(expected_withdrawals) < MAX_WITHDRAWALS_PER_PAYLOAD`. This can happen when `len(state.validators) < 16384` in mainnet due to how we calculate `validators_limit = min(len(state.validators), MAX_VALIDATORS_PER_WITHDRAWALS_SWEEP)`.
[2026-01-08T23:13:20.800000+00:00] nc1234: You can try to run v1.6.1's `capella/transition/core/pyspec_tests/transition_with_deposit_right_after_fork` with 1.7.0-alpha.0 spec and it should fail with mainnet preset
[2026-01-08T23:27:24.025000+00:00] jtraglia: Hey <@1035747431461167125> we discovered the same thing 😄  There's a thread about it here: https://discord.com/channels/595666850260713488/1458104535695495282/1458170734642856007
```

</details>
