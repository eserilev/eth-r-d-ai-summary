# Trusted Payments vs Non-Validating Builders

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion explores whether builders would prefer trusted payments over non-validating builders for resolving liquidity issues. nflaig suggests that if non-validating builders make top-ups instant, trusted payments might still be attractive because they free up block space (no need for proposer payment transactions) and can reach more validators through p2p networks, though they cannot trigger smart contract code in builder withdrawals.

potuz counters that the operational costs of maintaining deposits for trusted payments would likely outweigh the benefits. He argues that the 21K gas saved from not including proposer payment transactions is minimal compared to deposit costs, and that builders would only gain the tip from that freed space. Additionally, he notes that if all builders adopt this strategy, they end up including large deposit transactions in previous blocks anyway, effectively negating the block space advantage in the long term.

The discussion concludes with skepticism about the economic viability of trusted payments compared to non-validating builders, with no clear consensus on which approach builders would ultimately prefer.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T17:21:08.489000+00:00] nflaig: > In this case we need the proposer fee recipient ahead of time so we can simulate how much gas it will use to make the transfer (this varies if the fee recipient is a contract). We then can reserve this gas from the gas limit.
if we're resolving the liquidity issues and make top-ups instant by making builders non validating, then is there a reason why builders would prefer to use trusted payments? it would give you slightly more space in the block and also reach more validators in case they are not directly connected and only listen to p2p

one caveat ist that the builder withdrawal cannot trigger smart contract code which might be a limitation but haven't heard of anyone requiring that
[2025-12-15T17:27:50.890000+00:00] potuz: Topping up costs money
[2025-12-15T17:41:47.821000+00:00] nflaig: true, but would that maybe be offset by being able to not having to include the proposer payment tx in the block?
[2025-12-15T17:45:52.884000+00:00] potuz: very much doubt it in fact, even disregarding the operational  costs of having to top up regularly, the 21K gas vs the deposit has to be a big difference, the only gain the builder would have is the tip in the 21K space freed from it, plus, the builder is actually including a large transaction in the previous block, which  in the long term counts as including this transaction with the deposit in its own block (other builders will be including txs in the block if everyone follows this strategy)
```

</details>
