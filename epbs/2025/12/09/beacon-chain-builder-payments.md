# Builder Balance Check and Payment Processing Issues

**Channel:** epbs | **Date:** 2025-12-09

## Summary

**toki_yf** identified a potential issue in the builder payment processing system where `process_builder_pending_payments` lacks a balance check that exists in `process_execution_payload_bid`. Since these functions can execute up to 1 epoch apart, any balance decrease in the interim (due to penalties, withdrawals, or slashing) could result in payments being made with significantly less funds than originally promised in the bid. They noted that PR #4785 addresses this issue.

**jtraglia** disagreed with characterizing this as a "missing check," explaining that builders shouldn't be able to withdraw funds designated for payments, and that the system can pull from builders' full balance (not just excess) if they're slashed. However, they acknowledged that penalties could indeed result in less-than-expected payments to proposers and referenced this as a "builder payment grieving issue."

The discussion highlights an ongoing concern about the reliability of builder payments when validator balances fluctuate between bid acceptance and payment processing, though there's disagreement about whether additional checks are needed or if existing mechanisms are sufficient.

## Participants

- jtraglia
- toki_yf

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-09T10:48:18.093000+00:00] toki_yf: There is a missing check in `process_builder_pending_payments`

There is a balance check in `process_execution_payload_bid` (at acceptance time) but no balance check in `process_builder_pending_payments` even though this function is executed up to 1 epoch after the first one.

If there is any balance decrease between these two (penalties, partial withdrawals, slashing) the payments will be made with potentially far less funds than promised in the bid

Edit: https://github.com/ethereum/consensus-specs/pull/4785 solve this
[2025-12-09T13:44:44.789000+00:00] jtraglia: A builder shouldn't be able to withdrawal funds which will be used for payments. See [here](https://github.com/ethereum/consensus-specs/blob/daef46377cb5926af7d729fd533cbadc73fda117/specs/gloas/beacon-chain.md?plain=1#L818) it takes into account the builder payments above. If a builder is slashed, we [can pull from the builders balance](https://github.com/ethereum/consensus-specs/blob/daef46377cb5926af7d729fd533cbadc73fda117/specs/gloas/beacon-chain.md?plain=1#L785-L790), not just its excess balance. I've complained before that penalties could result in less-than-expected payments to proposers. I wouldn't say there's a missing check in `process_builder_pending_payments` though, unless I'm misunderstanding something.
[2025-12-09T17:32:39.019000+00:00] jtraglia: Builder payment grieving issue
```

</details>
