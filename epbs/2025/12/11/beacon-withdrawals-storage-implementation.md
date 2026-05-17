# Builder balance and withdrawal specifications

**Channel:** epbs | **Date:** 2025-12-11

## Summary

The discussion centers on a potential modification to builder balance specifications, specifically whether to remove `MIN_ACTIVATION_BALANCE` from the balance check in `process_execution_payload_bid`. Terencechain suggests this change would allow builders to utilize their full balance up to the ejection threshold, rather than being constrained by the minimum activation balance requirement.

nc1234 points out that implementing this change would require an additional modification from PR #4785 to prevent withdrawable balance from becoming zero, particularly if maintaining the current 0x03 validator design. This indicates the proposed change has dependencies that need to be addressed simultaneously to avoid unintended consequences with the withdrawal mechanism.

## Participants

- nc1234
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-11T14:42:52.607000+00:00] terencechain: regardless of the bigger change, does it make sense to remove `MIN_ACTIVATION_BALANCE` from `>= amount + pending_payments + pending_withdrawals + MIN_ACTIVATION_BALANCE` in `process_execution_payload_bid`, this seems like we can do this today, and allow builder to use up to all balances until ejection threshold is hit
[2025-12-11T15:39:31.862000+00:00] nc1234: If we want to achieve this, we also need the change from https://github.com/ethereum/consensus-specs/pull/4785/ or else the withdrawable balance will be 0. (If we want to keep the 0x03 validator design)
```

</details>
