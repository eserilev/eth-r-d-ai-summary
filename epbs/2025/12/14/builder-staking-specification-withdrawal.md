# Builder staking specification and withdrawal options

**Channel:** epbs | **Date:** 2025-12-14

## Summary

The discussion centers around a new builder staking specification that would create builders as a separate type of staking actor distinct from validators. Key changes include no activation queue for builders, instant top-up deposits, a minimum balance of 1 ETH (vs 32 ETH for validators), no partial withdrawals (only full exits), and immunity from slashing/penalties. This design enables faster deposits, lower staking requirements, and quicker builder payments since they bypass the standard exit queue and withdrawal delays.

The main debate focuses on three implementation options for handling withdrawals now that builders will be tracked separately from validators with their own indices. Option 1 would replace validator-specific fields with generic pubkey fields but would create significant boilerplate code for clients. Option 2 proposes using bitwise flags on indices to differentiate builders from validators - described as "hacky" but simpler to implement. Option 3 would create entirely separate withdrawal structures for builders, which is considered the least favorable approach due to added complexity.

No consensus was reached on which implementation approach to pursue, leaving this as an open technical decision that needs resolution.

## Participants

- jtraglia
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-14T01:16:31.514000+00:00] stefanbratanov: I have been a bit out of the loop since I am travelling, but option 2 on first looks decent, is there a TLDR comment somewhere on the change?
[2025-12-14T03:04:08.249000+00:00] jtraglia: TLDR: Builders have expressed that they prefer faster deposits & lower staking requirements. One solution to this is to make builders a new type of staking actor which do not perform validator duties.

In the PR we've made, builders will be tracked in a new `builders` list in the state. Some key changes: There is no activation queue for builders. Top-up deposits are instant, as soon as they hit the CL. The minimum builder balance is 1 ETH. Builders will not be able to perform partial withdrawals; only do full exits. Builders cannot be slashed nor penalized, which leads to a few nice simplifications for payments. Builder payments are faster since they don't need to go through the exit queue & min validator withdrawal delay.
[2025-12-14T03:09:22.124000+00:00] jtraglia: But now there's an implementation debate with a few options. Since builders are no longer validators & will be tracked in a separate list with a new set of indices, the withdrawals code will need to be changes. Options:

(1) Replace the `validator_index` field in `Withdrawal` & `PendingPartialWithdrawal` with a `pubkey` field that is generic enough to work for both; then rename `validator_pubkey` in `WithdrawalRequest` to `pubkey`. This would be pretty annoying for clients & result in a decent amount of boiler plate code.

(2) Keep the withdrawal structures the same and add a special bitwise flag to builder indices to differentiate them. This approach is a little hacky & confusing, but it would probably be simpler.

(3) Add new withdrawal structures specifically for builders. This would require new fields in the state to track them and processing functions. In my opinion, this approach is not a great idea.
```

</details>
