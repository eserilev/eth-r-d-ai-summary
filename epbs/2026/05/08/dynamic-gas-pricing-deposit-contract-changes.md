# Dynamic Gas Pricing and Deposit Contract Changes

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers around implementing exponential gas pricing for successive deposits within a block as a potential solution to an unspecified deposit-related issue. Potuz proposed making gas costs grow exponentially for multiple deposits, but mariusvanderwijden indicated this isn't possible with dynamic pricing and would require replacing the deposit contract through an irregular state transition that updates the contract code while preserving the same address.

While initially dismissed as a "no go," the participants acknowledged this approach would minimally affect user experience. If the contract were to be updated, barnabasbusa suggested implementing a simpler solution of limiting deposits per slot rather than making them expensive, and mariusvanderwijden noted the updated contract could potentially handle BLS verification directly.

The discussion leaves several open questions, including whether transaction pool queuing could provide an alternative solution and the broader feasibility of implementing such changes to the core deposit mechanism.

## Participants

- barnabasbusa
- mariusvanderwijden
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T10:12:51.043000+00:00] potuz: <@360491619402776577> is there a way to make the gas cost of successive deposits in the block grow exponentially? That would be the most elegant way of fixing this IMO
[2026-05-08T10:13:23.882000+00:00] potuz: Does dynamic pricing allow for this?
[2026-05-08T10:14:03.566000+00:00] mariusvanderwijden: replace the deposit contract
[2026-05-08T10:14:10.696000+00:00] mariusvanderwijden: Its not really possible
[2026-05-08T10:14:24.113000+00:00] potuz: Hmm this is a no go from the start lol
[2026-05-08T10:15:02.729000+00:00] barnabasbusa: yeah I really don't think replacing the deposit contract is possible
[2026-05-08T10:15:41.755000+00:00] nflaig: naive CL dev question, does replace here mean it must be a new contract or can you update the code (while keeping address the same) via irregular state transition at the fork?
[2026-05-08T10:15:58.180000+00:00] mariusvanderwijden: Update the contract code via irregular state transition
[2026-05-08T10:16:23.516000+00:00] mariusvanderwijden: We could even ask the contract to do the bls verification
[2026-05-08T10:16:29.672000+00:00] potuz: At least this would minimally affect UX
[2026-05-08T10:17:18.847000+00:00] barnabasbusa: if we change the contract, we might as well limit the number of deposits per slot ?
[2026-05-08T10:17:24.534000+00:00] barnabasbusa: so no need to make it expensive or anything
[2026-05-08T10:19:12.292000+00:00] barnabasbusa: is there no way to queue it up in the txpool?
```

</details>
