# Fork Choice Test Bug Fix

**Channel:** epbs | **Date:** 2026-04-20

## Summary

A user (nc1234) identified an issue with a fork choice test case called `on_execution_payload_envelope__wrong_withdrawals`, which they were failing despite passing all other tests. They observed that this test appeared identical to the valid version (`on_execution_payload_envelope__valid`) - using the same anchor state, block, and execution payload envelope - but expected different validity outcomes, suggesting a potential test specification bug.

In response to this discovery, nc1234 created PR #5133 to fix the issue in the Ethereum consensus specifications repository. The discussion was interrupted mid-message when terencechain began asking about Prysm's test results, but their complete question was cut off, leaving their specific inquiry unclear.

## Participants

- nc1234
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-20T05:49:11.030000+00:00] nc1234: Did you pass fork choice's `on_execution_payload_envelope__wrong_withdrawals` ? I pass everything except this. This looks identical to  `on_execution_payload_envelope__valid`, same anchor state, apply the same block, the same execution payload envelope, yet one expect it to be invalid, and the other one expect it to be valid
[2026-04-20T06:03:50.142000+00:00] nc1234: created a PR to fix this https://github.com/ethereum/consensus-specs/pull/5133
[2026-04-20T14:24:39.591000+00:00] terencechain: Did prysm pass fork choice's `on_
```

</details>
