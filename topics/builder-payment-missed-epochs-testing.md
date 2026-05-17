# Builder Payment After Missed Epochs Test Issue

---

## 2026-04-17 (epbs)

The team is experiencing issues with the `builder_payment_after_missed_epochs` test, specifically encountering a block root mismatch error. According to sproul, Claude (likely an AI assistant or automated system) has identified that the problem stems from the test expecting the payload hash to be written to `state.latest_block_hash` before the `process_slots` function is called, suggesting an ordering issue in the test implementation.

jtraglia acknowledged the issue and committed to investigating it further. The discussion remains open with no resolution yet reached, leaving the root cause analysis and potential fix as pending action items.

**Participants:** jtraglia, sproul

<details>
<summary>Raw messages</summary>

```
[2026-04-17T00:01:07.266000+00:00] sproul: we are having trouble with `builder_payment_after_missed_epochs`, getting a block root mismatch. Claude claims it's because the test expects the payload hash to be written to `state.latest_block_hash` before `process_slots` is called
[2026-04-17T00:05:12.808000+00:00] jtraglia: Let me look into this. Thanks for letting me know!
[2026-04-17T00:17:24.668000+00:00] jtraglia: Missed epochs test
```

</details>

