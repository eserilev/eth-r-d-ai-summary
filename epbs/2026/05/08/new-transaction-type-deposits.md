# New Transaction Type for Deposits

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers around introducing a new transaction type specifically for deposits to enable better rate limiting, similar to how blob transactions are currently handled. barnabasbusa proposed this approach as the simplest solution, drawing parallels to existing blob transaction filtering mechanisms.

The key technical challenge identified is that unlike blob transactions, current deposit transactions cannot be easily identified and limited without executing them, since they use regular transaction types. mariusvanderwijden explained that "we know outside of the txs if there are blobs" but "we don't know if the transaction will deposit" without execution. This reinforces the need for a dedicated deposit transaction type to enable pre-execution identification and counting.

potuz initially suggested an alternative approach involving longer exit churn periods for builders to make attacks costly, but acknowledged that a new transaction type would work for static counting of deposits. However, he noted that adding a new transaction type is "not trivial." The discussion reached consensus that a deposit transaction type would be necessary to implement the desired limiting functionality, though implementation complexity remains a concern.

## Participants

- barnabasbusa
- mariusvanderwijden
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T09:44:30.649000+00:00] barnabasbusa: could we introduce a new tx type for deposits? Then limit them by that? Similar to how we filter blob txs due to their new tx type
[2026-05-08T09:46:13.184000+00:00] potuz: I think the right thing to do is what I said originally to do: add a longer exit churn for builders: this makes the attack actually cost money at least
[2026-05-08T09:47:29.214000+00:00] potuz: Limiting at the EL seems not to be simple
[2026-05-08T09:47:59.202000+00:00] barnabasbusa: how are we limiting blob txs?
[2026-05-08T09:48:19.521000+00:00] potuz: Oh you mean statically count that type of txs
[2026-05-08T09:48:27.631000+00:00] barnabasbusa: yeah
[2026-05-08T09:48:29.035000+00:00] potuz: Yeah that would work
[2026-05-08T09:48:37.542000+00:00] barnabasbusa: would be the simplest imho
[2026-05-08T09:52:07.628000+00:00] potuz: Adding a new tx type is not trivial I think
[2026-05-08T09:55:04.906000+00:00] barnabasbusa: how would clients be able to 'ban' legacy deposit format?
[2026-05-08T10:19:37.238000+00:00] barnabasbusa: <@360491619402776577> how are you limiting number of blob txs ?
[2026-05-08T10:19:50.325000+00:00] barnabasbusa: and why can't we do the same for number of deposit txs?
[2026-05-08T10:20:03.546000+00:00] mariusvanderwijden: We know outside of the txs if there are blobs. We don't know if the transaction will deposit
[2026-05-08T10:20:16.207000+00:00] mariusvanderwijden: Deposit transaction type lfg
[2026-05-08T10:20:28.315000+00:00] potuz: Exactly, this would only work with a new tx type or similar
[2026-05-08T10:20:48.654000+00:00] potuz: Not reverting
[2026-05-08T10:21:12.421000+00:00] barnabasbusa: 
[2026-05-08T10:21:25.319000+00:00] barnabasbusa: yeah basically
[2026-05-08T10:21:37.177000+00:00] potuz: The precompute on the CL side is in the dead time of the slot
```

</details>
