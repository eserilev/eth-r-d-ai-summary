# Proposer preferences and bid forwarding

**Channel:** epbs | **Date:** 2025-12-13

## Summary

**kudeta** raised a question about whether forwarding the "current highest bid" would limit proposers' ability to express other preferences over p2p, such as requesting bids from non-censoring builders or those supporting specific preconfirmation protocols. They suggested that while starting with basic bid forwarding might be reasonable, proposers should ideally be able to express preferences in advance, which forwarding nodes could then validate against the preference set.

**nero_eth** responded that proposers with specific requirements (non-censoring/preconf builders) can establish direct connections to fetch bids from those builders, with the bid-over-p2p mechanism serving as a fallback option when direct connections fail. This suggests a hybrid approach where specialized preferences are handled through direct connections while p2p bid forwarding provides resilience.

## Participants

- kudeta
- nero_eth

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-13T13:16:46.659000+00:00] kudeta: Does forwarding the "current highest bid" imply that that the proposer can't express any other preferences about what / from whom they receive over p2p? Ideally they would be able to ask for bids from non-censoring builders that support e.g. a preconf protocol X. It might be reasonable just to start here, though, and iterate later. I guess if the proposer can express their preferences in advance then forwarding nodes could check other parts of the preference-set.
[2025-12-13T14:41:24.682000+00:00] nero_eth: Such proposers can just directly connect to non-censoring/preconf builders and fetch bids from those. The bid-over-p2p is more a fallback if the direct connections fail.
```

</details>
