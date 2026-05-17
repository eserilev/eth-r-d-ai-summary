# Builder Payment After Missed Epochs Test Issue

**Channel:** epbs | **Date:** 2026-04-17

## Summary

The discussion centers around issues with the `builder_payment_after_missed_epochs` spec test, which covers scenarios where there's a gap of 2+ missed epochs between a parent block and the latest slot, requiring proper builder charging and proposer payment. Sproul reported a block root mismatch error, while terencechain discovered a bug in Prysm related to this scenario but found that spec tests were still passing, indicating the tests themselves were broken.

The team confirmed that the spec test was indeed broken (referenced in PR #5120), with potuz noting that "if you pass the tests the implementation must be buggy." The test was recently added to cover this previously untested scenario, but contained errors that prevented it from properly validating implementations. Nflaig pointed to the commit where the test was originally added, and terencechain shared the specific code logic for handling builder pending withdrawals when `parent_bid.value > 0`.

Jtraglia took action to generate a fixed version of the test and provided links to both the corrected test and a full test suite run that includes a field swap change in the state. The immediate action item is for the team to confirm the fixed test works properly before merging the PR with the corrections.

## Participants

- jtraglia
- nflaig
- potuz
- sproul
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-17T00:01:07.266000+00:00] sproul: we are having trouble with `builder_payment_after_missed_epochs`, getting a block root mismatch. Claude claims it's because the test expects the payload hash to be written to `state.latest_block_hash` before `process_slots` is called
[2026-04-17T00:17:24.668000+00:00] jtraglia: Missed epochs test
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
[2026-04-17T17:38:17.635000+00:00] jtraglia: <@363800010518822915> I will generate the fixed test. I'll post it here in just a minute.
[2026-04-17T17:43:23.796000+00:00] jtraglia: 
[2026-04-17T17:45:21.348000+00:00] jtraglia: And then this run will contain the full suite of tests on top of the latest master:
https://github.com/jtraglia/consensus-specs/actions/runs/24578808301
[2026-04-17T17:46:11.661000+00:00] jtraglia: Note, this does include the field swap change in the state.
[2026-04-17T17:46:47.541000+00:00] jtraglia: If you can confirm this works, I'll merge the PR with the fix.
```

</details>
