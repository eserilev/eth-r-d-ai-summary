# Fork Choice Test Bug Fix

---

## 2026-04-20 (epbs)

A developer (nc1234) identified an issue with a specific fork choice test case called `on_execution_payload_envelope__wrong_withdrawals`. They were passing all other fork choice tests except this one, which appeared to be incorrectly configured. The problematic test was identical to the valid test case `on_execution_payload_envelope__valid` in every aspect - same anchor state, same block application, and same execution payload envelope - yet it expected an invalid result while the other expected valid.

The developer created PR #5133 in the ethereum/consensus-specs repository to fix this test bug, suggesting the issue was with the test specification rather than their implementation.

**Participants:** nc1234

<details>
<summary>Raw messages</summary>

```
[2026-04-20T05:49:11.030000+00:00] nc1234: Did you pass fork choice's `on_execution_payload_envelope__wrong_withdrawals` ? I pass everything except this. This looks identical to  `on_execution_payload_envelope__valid`, same anchor state, apply the same block, the same execution payload envelope, yet one expect it to be invalid, and the other one expect it to be valid
[2026-04-20T06:03:50.142000+00:00] nc1234: created a PR to fix this https://github.com/ethereum/consensus-specs/pull/5133
```

</details>

---

## 2026-05-04 (epbs)

.paulharris raised a question about the viability of pure fork choice type tests, noting that they had likely been previously considered. They suggested that implementing such tests would probably be challenging when working with disparate (different) implementations across the Ethereum ecosystem.

This appears to be an initial inquiry rather than a detailed discussion, with no responses or further elaboration provided. The feasibility concerns around fork choice testing across multiple client implementations remains an open question without any conclusions or next steps identified in this brief exchange.

**Participants:** .paulharris

<details>
<summary>Raw messages</summary>

```
[2026-05-04T17:08:00.584000+00:00] .paulharris: I’m assuming we’ve previously looked at the viability of pure fork choice type tests… probably hard against disparate implementations I guess
```

</details>

