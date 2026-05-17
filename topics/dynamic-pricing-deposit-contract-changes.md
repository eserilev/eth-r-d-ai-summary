# Dynamic Pricing and Deposit Contract Changes

---

## 2026-05-08 (epbs)

The discussion centers around potential solutions for limiting deposits per block through dynamic pricing mechanisms. Potuz proposes making gas costs for successive deposits within a block grow exponentially, questioning whether dynamic pricing systems could support this approach. The conversation explores updating the existing deposit contract via irregular state transition at a fork rather than deploying a new contract, with mariusvanderwijden noting they could even move BLS verification into the contract itself.

Alternative approaches are debated, including directly limiting deposits per slot at the contract level versus using pricing mechanisms. Concerns are raised about scalability with gas limit increases, as this would require ongoing optimization for both execution layer (EL) and consensus layer (CL) performance. The discussion touches on user experience implications, noting that contract reverts when hitting limits would be problematic, and explores whether transactions could be queued in the transaction pool instead.

The conversation concludes with potuz noting that since ePBS (enshrined Proposer-Builder Separation), various deadlines including PTC (Payload Timeliness Committee), attestations, and data availability are now interconnected, adding complexity to any proposed solutions.

**Participants:** barnabasbusa, mariusvanderwijden, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T10:12:51.043000+00:00] potuz: <@360491619402776577> is there a way to make the gas cost of successive deposits in the block grow exponentially? That would be the most elegant way of fixing this IMO
[2026-05-08T10:13:23.882000+00:00] potuz: Does dynamic pricing allow for this?
[2026-05-08T10:15:41.755000+00:00] nflaig: naive CL dev question, does replace here mean it must be a new contract or can you update the code (while keeping address the same) via irregular state transition at the fork?
[2026-05-08T10:15:58.180000+00:00] mariusvanderwijden: Update the contract code via irregular state transition
[2026-05-08T10:16:23.516000+00:00] mariusvanderwijden: We could even ask the contract to do the bls verification
[2026-05-08T10:16:29.672000+00:00] potuz: At least this would minimally affect UX
[2026-05-08T10:16:30.830000+00:00] nflaig: but you would have to optimize this indefinitely with gas limit increases
[2026-05-08T10:16:50.636000+00:00] potuz: As with anything else isn't it?
[2026-05-08T10:17:18.847000+00:00] barnabasbusa: if we change the contract, we might as well limit the number of deposits per slot ?
[2026-05-08T10:17:24.534000+00:00] barnabasbusa: so no need to make it expensive or anything
[2026-05-08T10:17:38.408000+00:00] nflaig: so far, gas limit increase was mostly about benchmarking the EL, now we also need to benchmark the CL
[2026-05-08T10:18:22.643000+00:00] potuz: The contract can revert if the tx hits a limit but that sucks for the user
[2026-05-08T10:19:12.292000+00:00] barnabasbusa: is there no way to queue it up in the txpool?
[2026-05-08T10:19:22.729000+00:00] potuz: Since ePBS this is joint, PTC, attestation deadline and da deadlines are tied
```

</details>

