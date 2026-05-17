# Client stability testing and missed slots discussion

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around client stability testing using Kurtosis, with concerns about potential missed slots during deployment. Potuz inquires about testing with missed slots across different clients, while barnabasbusa reports that two clients have shown instability and may produce missed slots. However, 0xunclebill provides reassurance by confirming successful Kurtosis testing with intentional missed slots using Lighthouse, Prysm, and Lodestar clients.

The conversation reveals some uncertainty about deposit/withdrawal capabilities, with clarification that deposits are possible but withdrawals are not. Potuz suggests that if Nimbus and Teku clients aren't ready at genesis, they can be added via deposits later. The team ultimately decides to proceed with the current setup despite stability concerns, with Potuz expressing particular worry about "builder ones" but adopting a pragmatic approach that if the system fails at launch, minimal harm would be done since "the EF is rich anyway."

## Participants

- 0xunclebill
- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T12:54:13.044000+00:00] potuz: Is this going to cause missed slots? have you run kurtosis with missed slots already and the other three clients?
[2026-03-04T12:54:41.163000+00:00] barnabasbusa: yeah the other 2 clients have not been the most stable
[2026-03-04T12:54:43.521000+00:00] potuz: If we had one happy run before I suspect it would be better, if Nimbus and Teku are not ready at genesis they can always be deposited
[2026-03-04T12:54:49.382000+00:00] barnabasbusa: they might produce some missed slots
[2026-03-04T12:54:54.143000+00:00] 0xunclebill: i ran kurtosis today with intentional missed slots and we seemed to be okay
[2026-03-04T12:54:58.107000+00:00] 0xunclebill: lh prysm and lodestar
[2026-03-04T12:55:00.642000+00:00] barnabasbusa: I thought we can't do deposits
[2026-03-04T12:55:07.219000+00:00] potuz: we can do deposits
[2026-03-04T12:55:13.809000+00:00] barnabasbusa: ah we can't do withdrawals?
[2026-03-04T12:55:30.648000+00:00] potuz: the only thing that worries me is builder ones, anyway, let's ship it as is, if we are dead at launch no harm done
[2026-03-04T12:55:33.870000+00:00] potuz: the EF is rich anyway
[2026-03-04T12:57:53.571000+00:00] barnabasbusa: https://klipy.com/gifs/these-people-think-im-made-out-of-money-bro
```

</details>
