# Builder Payment Test Coverage and Spec Issues

---

## 2026-04-17 (epbs)

Terence identified a potential bug in Prysm related to builder payments when there's a gap of 2+ missed epochs between a full parent block and the latest slot. The issue involves scenarios where the proposer should be paid and the builder charged, but existing spec tests weren't catching this edge case.

Nflaig confirmed that a test called `test_builder_payment_after_missed_epochs` was added to cover this scenario, but noted it appears to be broken according to PR #5120. Potuz emphasized that implementations passing the current spec tests must be buggy, indicating the tests themselves had issues. The problematic code involves the `builder_pending_withdrawals` logic that should handle builder payments when `parent_bid.value > 0`.

The test coverage was added in a recent commit (PR #5094), but since the spec itself contained errors until recently, implementations could pass tests while having incorrect behavior. Terence wants to ensure proper test coverage exists going forward to catch this missed epochs payment scenario.

**Participants:** nflaig, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-17T17:19:27.752000+00:00] terencechain: hi, do we have a spec test that covers a scenario that there's a missed epoch gap of >= 2 epochs btw a full parent block and latest slot, the builder is paid having its payload included on chain? I think i found this bug in prysm (i haven't folloed the latest commit) but yet i was passing spec tests as of few days ago
[2026-04-17T17:22:16.927000+00:00] nflaig: what you mean by "builder is paid", you mean charged?
[2026-04-17T17:22:41.896000+00:00] terencechain: sorry i meant proposer is paid and builder is charged
[2026-04-17T17:22:59.691000+00:00] nflaig: `test_builder_payment_after_missed_epochs` should cover that, it was added later
[2026-04-17T17:23:20.122000+00:00] nflaig: but also it seems broken https://github.com/ethereum/consensus-specs/pull/5120
[2026-04-17T17:23:47.567000+00:00] terencechain: Hm great, i guess it wasn't there a few days ago. I didnt implement yet passing spec tests
```
  elif parent_bid.value > 0:
      state.builder_pending_withdrawals.append(
          BuilderPendingWithdrawal(
              fee_recipient=parent_bid.fee_recipient,
              amount=parent_bid.value,
              builder_index=parent_bid.builder_index,
          )
      )
```
[2026-04-17T17:24:15.745000+00:00] nflaig: the spec was not correct few days ago
[2026-04-17T17:24:39.104000+00:00] potuz: You can't pass spectest with a good implementation
[2026-04-17T17:24:53.720000+00:00] potuz: That is, if you pass the tests the implementation must be buggy
[2026-04-17T17:25:32.901000+00:00] terencechain: but there wasn't any spec test to cover this scenario (few days ago), at least the spec test was borken it seems
[2026-04-17T17:25:42.754000+00:00] terencechain: i just want to make sure such test is added now and will be there later
[2026-04-17T17:25:52.585000+00:00] nflaig: it was added in this [commit](https://github.com/ethereum/consensus-specs/pull/5094/changes/0923d8abaaafca007ecea16884fcc58f15666552)
```

</details>

