# PTC Duties Calculation and Lookahead Discussion

**Channel:** epbs | **Date:** 2026-01-28

## Summary

The discussion centers on a technical issue with PTC (Payload Timeliness Committee) duties calculation in Ethereum. pk910 identified that PTC duties for epoch N can only be calculated after the epoch N state transition because they require weighted selection based on effective balances that may change during the transition, unlike attester duties which can be calculated in epoch N-1. This creates a "lookahead problem" where validators don't know their PTC duties in advance.

Several solutions were proposed but faced tradeoffs. ralexstokes suggested treating PTC duties like proposer shuffling to avoid the lookahead issue, but terencechain and jtraglia noted this would require adding new state fields, which seems undesirable. The participants clarified that lookahead was originally implemented for based pre-confirmation protocols and to allow validators to sell rights in advance, but these needs don't apply to PTCs. potuz mentioned that just-in-time (JIT) computation is particularly annoying for implementations like Prysm when accessing effective balances.

Alternative approaches were discussed including using VRF election (though this would require removing PTC votes from beacon blocks) or selecting simple subsets of attesters rather than stake-weighted selection. However, fradamt emphasized that stake-weighted selection is important for security given the small committee size. The discussion concluded without a clear resolution, with the group seeming to lean toward accepting the JIT computation despite its implementation complexity, while noting that a more comprehensive solution might involve keeping delayed copies of effective balances for all similar use cases.

## Participants

- .paulharris
- fradamt
- jtraglia
- pk910
- potuz
- ralexstokes
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-28T16:29:15.361000+00:00] pk910: Heya,
Is it correct, that PTC duties for epoch N can only be calculated after the epoch N state transition?
attester duties can be calculated in epoch N-1 already, but PTC duties do a weighted selection which is based on the effective balances that may change during the epoch transition. 
did I understand the spec correctly here?
[2026-01-28T16:33:01.036000+00:00] ralexstokes: we should treat them how we do the proposer shuffling so we dont have this lookahead problem
[2026-01-28T16:42:11.024000+00:00] terencechain: wouldn't that require adding a lookahead field to the state? That does not seem ideal.
Also, what is the issue with having PTC duties known at epoch N and computed during the epoch transition?
[2026-01-28T16:43:06.300000+00:00] jtraglia: Good points. It would require a new state field & it's probably not worth it.
[2026-01-28T16:44:11.734000+00:00] pk910: I think the reason for the lookahead is to give validators some time to prepare for their duties.
with the current design,  a PTC contributor in the first slot of an epoch doesn't know about his duty before the first block is proposed
[2026-01-28T16:46:28.061000+00:00] terencechain: the reason lookahead was for based pre-confirmation protocols rely on a deterministic proposer schedule for smooth operations, otherwise i dont think we would have done it
[2026-01-28T16:50:17.907000+00:00] potuz: the reason for the lookahead is that validators may want to sell in advance certain rights, there's no need for that in the PTC
[2026-01-28T16:50:31.217000+00:00] potuz: oh jinx to Terence 🙂
[2026-01-28T16:52:54.345000+00:00] ralexstokes: it also reduces implementation complexity -- its just fewer moving pieces that can change just-in-time
[2026-01-28T16:53:20.282000+00:00] ralexstokes: agree we shouldn't add more to the state but it does seem a bit off that we just patched this type of thing and are turning around and re-adding it
[2026-01-28T16:55:47.461000+00:00] terencechain: naively speaking, i think that increaes implementation complexiity.. new state field.. new epoch transition method 🙂 but could be wrong here
[2026-01-28T17:00:08.046000+00:00] ralexstokes: for the state transition yes, but there are many other things that get simpler bc the data can't change at the last minute
[2026-01-28T17:01:18.789000+00:00] ralexstokes: i think if we wanted a proper solution here would would keep a delayed copy of effective balances
[2026-01-28T17:01:36.321000+00:00] ralexstokes: that should cover all of the use cases, rather than just addressing these one-by-one
[2026-01-28T17:02:05.944000+00:00] ralexstokes: but i dont think we should go down that road now, and so then yeah the PTCs will be JIT
[2026-01-28T17:33:22.975000+00:00] fradamt: Imho having different PTCs if there's a small fork at epoch boundary is quite annoying and an in-state lookahead seems preferable. The proposer being potentially different with a small fork was always a bad design, even before preconfirmations
[2026-01-28T17:35:41.997000+00:00] fradamt: although to be fair the PTC should only change a little bit given bounded balance changes, it shouldn't completely change? (should check the actual logic, I am not sure if this holds)
[2026-01-28T17:36:16.562000+00:00] fradamt: In which case, I think it's a lot better, the only issue would then be the JIT computation
[2026-01-28T17:38:06.474000+00:00] fradamt: Another option (but probably getting ahead of ourselves here) would be to make it a VRF election, like for aggregators, which is also more secure. We'd have to remove the PTC votes from the beacon block though, because they the selection proofs wouldn't be aggregable.
[2026-01-28T18:18:28.458000+00:00] potuz: The JIT computation is indeed annoying, in general anything that requires access to the effective balance is quite annoying for Prysm. If we sampled by the effective balance of the justified checkpoint it would be simpler,  but probably bug prone though
[2026-01-28T21:38:29.403000+00:00] .paulharris: so, naive question...
if we wanted ptc to be stable, we could just select a simple subset of the attesters like 'first n from each committee', and then its deterministic given a list of attesters... we could avoid extra state info etc if we wanted a stable ptc...
[2026-01-28T22:25:48.854000+00:00] fradamt: The issue is that we want the selection to be stake-weighted, not just a shuffle, because it's a small committee and it's much more secure that way
[2026-01-28T23:06:34.514000+00:00] .paulharris: it just seems like stake weighting is what will stop you from being able to have a stable PTC, so if you wanted a stable PTC theres alternatives is all
```

</details>
