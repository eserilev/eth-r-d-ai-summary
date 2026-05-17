# Builder deposits and state management concerns

**Channel:** epbs | **Date:** 2025-12-13

## Summary

The discussion centers on managing state bloat concerns with 1ETH builder deposits in `state.builders`. nc1234 proposed adding a `last_won_bid_epoch` field to periodically evict inactive builders, but potuz expressed concern about introducing such changes late in the process with quick deposits, preferring to keep the scope minimal and defer improvements to the next fork. terencechain agreed with keeping builder balances under the builder field rather than separate lists for simplicity.

A major concern raised by terencechain involves the proposed changes to `PendingPartialWithdrawal`, `Withdrawal`, and `WithdrawalRequest` types, which would move from validator index-based to validator pubkey-based systems. This change would require new Golas types and modifications to all state transition code using these types, potentially causing significant scope creep in client implementations. jtraglia defended this approach, arguing that the alternative of creating separate queues with new types for builders would be worse.

The discussion concluded with jtraglia reporting that all tests are now passing after recent fixes and cleanup, including the removal of unnecessary tests for slashed builders. Work continues with jtraglia planning to resume development after a break.

## Participants

- jtraglia
- nc1234
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-13T00:15:27.174000+00:00] nc1234: Regarding the concern that 1ETH deposits will bloat `state.builders`, can we add a field something like `last_won_bid_epoch` to `Builder`, and we periodically evict builders from `state.builders` that haven't been winning a single bid in the last x epochs?
[2025-12-13T00:15:47.647000+00:00] potuz: I thought of this but with quick deposits it may surface weird bugs. We're changing a large thing late in the process. If we keep the scope minimal it's better IMO. We can always add reuse in the next fork
[2025-12-13T04:49:33.760000+00:00] terencechain: I took a first look, left some feedback, and I am fine keeping balance under builder field instead of a separate list. I do not think the performance impact is meaningful and I would prefer sanity here...

One concern I do have is have we seriously considered not touching `PendingPartialWithdrawal`, `Withdrawal`, and `WithdrawalRequest` at all? The spec moves these from validator index based to validator pubkey, which means more new Gloas types and touching every state transition code that uses with them. The scope may seem ok in spec, but such complexity could easily cascade in client code and turn into a huge scope creep
[2025-12-13T05:17:53.614000+00:00] jtraglia: Yeah I have thought about it. The alternative is worse in my opinion. We'd have to create separate queues (with new types) in the state just for builders.
[2025-12-13T05:18:27.405000+00:00] jtraglia: Also I just fixed the last batch of tests. Everything's still very messy but I believe all tests should pass now.
[2025-12-13T05:18:45.520000+00:00] jtraglia: I also got to delete a few unnecessary ones which was nice (eg slashed builders).
[2025-12-13T05:23:36.502000+00:00] jtraglia: <@520034910149410861> I'm about to call it a night. Please make whatever improvements you want 🙂  I'll continue working on this in ~10 hours.
```

</details>
