# Bid forwarding and 1 ETH deposit concerns

**Channel:** epbs | **Date:** 2025-12-12

## Summary

The discussion centers around concerns with a 1 ETH deposit mechanism for bid forwarding in Ethereum. Fradamt initially worried that low-cost deposits could enable drowning out honest bids in peer-to-peer networks, but concluded that only forwarding the currently highest bid should be sufficient regardless of bid volume, since payment enforcement makes the concept of "honest bids" less relevant.

Potuz suggested forwarding the highest paying bid only when it increases by more than a threshold (x ETH or x%), though noted that since only one bid per validator is forwarded, x=0 might be adequate. The main concern shifted to potential state bloat from 1 ETH deposits, with clarification that while similar deposits are possible today, the economic constraints differ because withdrawing requires first activating validators (needing additional 3.1M ETH). 

The proposed solution involves implementing a very large exit window for validators, based on the assumption that most builders wouldn't want to exit anyway. Open questions remain around optimal rate limiting mechanisms and whether contract-side limits on deposited builders would be beneficial.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T15:19:40.146000+00:00] fradamt: I was  temporarily worried about the 1 ETH thing because of being able to drown out honest bids in p2p, but I guess the concept of "honest bid" is somewhat pointless when the payment is enforced. Just only forwarding the currently highest bid should be perfectly fine regardless of how many bids are theoretically floating around
[2025-12-12T15:20:02.123000+00:00] fradamt: (I think this was well understood already, just repeating it in case someone else had the same concern)
[2025-12-12T15:24:41.911000+00:00] potuz: we where thinking on forwarding highest paying as long as it increases the bid by > x, or x% where x is to be determined. But since we only forward a single bid per validator, perhaps x = 0 is already good enough
[2025-12-12T15:25:03.287000+00:00] potuz: What worries me about the 1ETH is the possible state bloat, I didn't understand your explanation to be the same as today
[2025-12-12T15:34:24.675000+00:00] fradamt: Can I not make e.g. 100k 1 ETH deposits today? Is the difference that, though the state bloat is ~the same, that the cost and budget are actually much lower because I would then not be able to withdraw those validators without first activating them, which would require another 3.1M ETH?
[2025-12-12T15:36:02.815000+00:00] fradamt: Anyway, what would be a good solution, assuming we're not constrained by the deposit contract? Just contract-side rate limits on the amount of deposited builders?
[2025-12-12T15:42:01.868000+00:00] potuz: I still don't understand, ahhhh I think I do, you mean depositing just 1 ETH to create a validator, but yeah you can't withdraw it
[2025-12-12T15:42:34.379000+00:00] potuz: I think the solution should be have a very very large window for exits
[2025-12-12T15:43:02.119000+00:00] potuz: Most builders would not want to exit anyway
```

</details>
