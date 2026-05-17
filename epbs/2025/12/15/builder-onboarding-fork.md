# Builder Onboarding at Fork

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion centers on a proposed mechanism for onboarding builders at a fork by automatically converting existing 0x03 validators. The proposal involves exiting active 0x03 validators at the fork, creating builders with identical pubkeys and withdrawal credentials, transferring 1 ETH to the builder while scheduling the remaining ETH for withdrawal, and making builders immediately active. For future deposits to pubkeys with 0x03 withdrawal credentials, the system would create new builders if no active validator exists, or deposit to existing validators/builders while maintaining the constraint that no two active validators can share the same pubkey.

Several technical concerns emerged during the discussion. jtraglia pointed out that validators wouldn't be immediately exited but queued for exit, creating a period where both active builders and validators would coexist. potuz expressed worry about the complexity introduced by potential reorgs at the fork boundary, noting that finalization requirements exist to avoid messy pubkey-to-index cache reordering issues. There was also discussion about whether the 15-minute gap without builders would significantly impact DeFi activity, with references to previous circuit breaker incidents suggesting minimal migration to mempool-based gas auctions.

The conversation revealed disagreement about deposit handling, with nc1234 questioning why deposits to existing 0x01/0x02 validators wouldn't trigger conversion to builders, while potuz clarified this follows current deposit behavior where funds are simply added to existing validators regardless of withdrawal credentials. The overall complexity and edge cases around reorgs appeared to be causing concern about the feasibility of the approach.

## Participants

- jtraglia
- l03443
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T23:26:56.281000+00:00] potuz: Mechanism to onboard builders at the fork:

1. Existing 0x03 validators at the fork are exited (if active)
2. If the validator was active. A builder with the exact same pubkey and withdrawal credential is put in the builder slice. 
3. 1 ETH is transferred from the validator to the builder. The remaining ETH is scheduled for withdrawal as usual. 
4. Builder is active immediately. 

At any given time for a deposit for a pubkey with a withdrawal credential 0x03... the following can happen:

1. No active validator has this pubkey: create the builder, deposit.
2. One active validator has this pubkey. If it is a 0x01 or 0x02 validator, deposit to it, ignore the withdrawal credential. 
3. One active validator has this pubkey. If it is a 0x03, it has to be a builder by the above, so just deposit to it. 

At no point there can be two active validators with the exact same pubkey
[2025-12-15T23:28:50.893000+00:00] potuz: The question here is how important it is to have builders already onboarded at the fork: 15 minutes of Defi seems to be a hell lot. I doubt activity will move to gas price auctions on the mempool
[2025-12-15T23:29:43.462000+00:00] l03443: iirc <@911055007837720597> shared some stats around this when the circuit breaker triggered after fusaka. Dont think much moved to the mempool
[2025-12-15T23:29:51.420000+00:00] l03443: so would be a similar situation i guess
[2025-12-15T23:32:25.880000+00:00] potuz: yeah... and just to be clear, I haven't looked at the spec yet to judge here, but I assume that the finalization requirement is put in place for the same reason as for deposits: dealing with reorgs that change the order of the pubkey to index cache is a mess for clients, so we definitely want to onboard them after finalization
[2025-12-15T23:33:31.582000+00:00] potuz: hmm, then the problem of a reorg across the fork boundary has the same issue
[2025-12-15T23:33:33.424000+00:00] jtraglia: One issue with this. If there's 0x03 validator at the fork, it wouldn't be **immediately** exited. It would be queued for exit. So there would be an active builder & validator at the fork.
[2025-12-15T23:33:40.309000+00:00] potuz: yeah, this starts to creep into complexity that scares me
[2025-12-15T23:35:30.287000+00:00] potuz: this was with regards of reorgs at the fork, not that worried about exiting validators and active builders...
[2025-12-15T23:39:41.290000+00:00] nc1234: > 2. One active validator has this pubkey. If it is a 0x01 or 0x02 validator, deposit to it, ignore the withdrawal credential.
Why would this scenario deposit to the validator? I would take this as a signal to exit validator and convert into builder like the flow you said about onboarding builders at fork
[2025-12-15T23:49:24.857000+00:00] potuz: Because that's how deposits work today: if you deposit to an existing validator you just add to it, doesn't matter your withdrawal credentials
```

</details>
