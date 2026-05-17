# Spec tests for builder withdrawals coverage

**Channel:** epbs | **Date:** 2026-01-30

## Summary

This discussion centers around spec tests for builder withdrawals functionality in Ethereum consensus clients. Terencechain inquired about the existence of spec tests covering builder pending withdrawals and sweep withdrawals. Jtraglia confirmed that comprehensive withdrawal tests were added in [PR #4830](https://github.com/ethereum/consensus-specs/pull/4830) and are included in the nightly reference tests, meaning clients passing these tests should have proper coverage.

However, when terencechain checked their Prysm client implementation using nightly tag `nightly-21422848633`, they found builder sweeping withdrawals tests but couldn't locate any builder pending partial withdrawals tests, reporting zero partial withdrawal counts in their logs. Jtraglia verified the nightly tag was correct (from 3 days prior) and pointed to specific test cases like `test_single_builder_withdrawal` that should include builder pending withdrawals functionality.

The discussion ends with terencechain planning to double-check their code coverage and re-examine the test results, as there appears to be a discrepancy between the expected test coverage and what they're observing in their client implementation.

## Participants

- jtraglia
- leolara
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-30T02:49:31.114000+00:00] terencechain: did we ever add spec tests for builder pending withdrawals and sweep withdrawals?
[2026-01-30T04:03:23.589000+00:00] jtraglia: Yes, <@529978984902164490> added many new withdrawals tests for that stuff.
https://github.com/ethereum/consensus-specs/pull/4830
[2026-01-30T04:04:03.229000+00:00] jtraglia: So if you're passing the nightly reference tests, that means you're passing these too 🙂
[2026-01-30T04:06:12.117000+00:00] terencechain: Thanks I'll double check tomorrow they are covered in prysm via code coverage
[2026-01-30T10:51:12.311000+00:00] leolara: let me know if there are any test that you think is missing
[2026-01-30T18:32:25.804000+00:00] terencechain: Nice, im on `nightly-21422848633`
I see builder sweeping withdrawals, but i dont see any builder pending partial withdrawals
[2026-01-30T18:45:06.906000+00:00] jtraglia: There should be builder pending withdrawals. Eg `test_single_builder_withdrawal`
[2026-01-30T18:45:51.920000+00:00] jtraglia: 
[2026-01-30T18:47:40.525000+00:00] jtraglia: Where:
[2026-01-30T18:47:42.179000+00:00] jtraglia: 
[2026-01-30T18:49:16.500000+00:00] terencechain: Is my nightly tag correct? Sorry on phone now and can't check
[2026-01-30T18:50:34.876000+00:00] jtraglia: Yes, it is. That's from 3 days ago: https://github.com/ethereum/consensus-specs/actions/runs/21422848633
[2026-01-30T18:50:59.527000+00:00] jtraglia: It should contain all of the new withdrawal tests.
[2026-01-30T19:03:16.811000+00:00] terencechain: Thanks I'll check again. So far I logged the partial withdrawal count and they were 0
```

</details>
