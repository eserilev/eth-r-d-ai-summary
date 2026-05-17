# Builder Withdrawal Epoch Validation Bug Fix

**Channel:** epbs | **Date:** 2025-12-04

## Summary

A bug fix was identified and submitted for the builder withdrawal epoch validation logic in the Ethereum consensus specifications. The issue, reported by toki_yf in pull request #4774, involves incorrect epoch validation where the code was checking if the epoch is in the future rather than checking if it has already passed.

The fix appears to be a straightforward correction to the validation logic, though no additional discussion or consensus from other contributors is visible in this single message thread.

## Participants

- toki_yf

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-04T23:06:35.505000+00:00] toki_yf: little fix here: https://github.com/ethereum/consensus-specs/pull/4774
I think we were checking the wrong thing for the builder withdrawal (if the epoch is in the futur instead of passed)
```

</details>
