# Builder balance tracking implementation details

**Channel:** epbs | **Date:** 2025-12-12

## Summary

The discussion centers on implementation choices for tracking builder balances, with potuz identifying two performance concerns: balance tracking in the builder structure and passing pubkeys to helper functions. The main debate focuses on whether to use pubkeys or builder indices in pending builder withdrawals, with jtraglia having converted to pubkeys to simplify the `get_pending_balance_to_withdraw` function, while potuz argues this is likely a poor performance choice.

Regarding a separate `state.builder_balances` field (similar to validator balances to avoid hashing), the participants initially considered it but concluded it's probably acceptable to keep balances in the builder structure since only a single builder's balance changes per slot. However, potuz remains convinced that using pubkeys instead of indices is problematic from a performance perspective.

The discussion ends with jtraglia agreeing to reconsider the pubkey approach and involve another team member in the decision, noting that reverting this change would be straightforward if needed.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T17:14:52.442000+00:00] potuz: How do implementers feel about tracking balances in the builder structure? cc <@641548687813902336> <@363800010518822915>
[2025-12-12T17:25:30.845000+00:00] potuz: A quick read and I find two performance hit: one is the balance in the builder, this may not be so problematic, but passing pubkeys to helpers is
[2025-12-12T17:25:53.932000+00:00] potuz: I think you should keep the builder index in the pending builder withdrawal
[2025-12-12T17:26:03.810000+00:00] potuz: I dont see why you need to move to pubkeys
[2025-12-12T17:27:46.764000+00:00] jtraglia: I converted it to use pubkeys because it simplified `get_pending_balance_to_withdraw`:
[2025-12-12T17:27:47.990000+00:00] jtraglia: 
[2025-12-12T17:28:10.148000+00:00] potuz: yeah I see that, but I think that's most probably a bad choice
[2025-12-12T17:30:17.318000+00:00] jtraglia: Maybe. We can change this if we want.
[2025-12-12T17:40:35.758000+00:00] potuz: oh  I realized I never left these comments
[2025-12-12T18:10:10.764000+00:00] jtraglia: <@755590043632140352> why do you want this? I see no benefit in a separate field for builder balances.
[2025-12-12T18:11:03.600000+00:00] potuz: Same as for validator avoid hashing.
[2025-12-12T18:11:40.472000+00:00] jtraglia: Hmm I see.
[2025-12-12T18:12:16.821000+00:00] jtraglia: Does it make a difference that only a single builder's balance would change each slot?
[2025-12-12T18:24:42.190000+00:00] jtraglia: But yeah, if that turns out to be really inefficient, we will add a new `state.builder_balances` field.
[2025-12-12T18:31:25.378000+00:00] potuz: Nah it's probably fine as is
[2025-12-12T18:32:03.435000+00:00] potuz: The other one of the pubkey I'm pretty sure it's a bad idea
[2025-12-12T18:33:43.420000+00:00] jtraglia: Yeah I'll think about this some & have <@520034910149410861> weigh in too. Reverting this would be easy.
```

</details>
