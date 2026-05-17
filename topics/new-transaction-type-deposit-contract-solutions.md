# New transaction type and deposit contract solutions

---

## 2026-05-08 (epbs)

The discussion centers on addressing a potential attack vector involving mass deposits that could overwhelm consensus layer (CL) processing. Potuz initially suggests adding longer exit churn for builders to make attacks more expensive, while the team explores various technical solutions. They compare the approach to how blob transactions are limited, noting that unlike blob txs which are identifiable outside of transaction execution, deposit transactions can only be identified after execution, making static counting difficult.

Several mitigation strategies are debated, including creating a new deposit transaction type for easier limiting, implementing exponential gas pricing for successive deposits, and replacing/updating the deposit contract via irregular state transition. However, replacing the deposit contract is deemed challenging, and exponential gas pricing appears technically difficult to implement. The team notes that under ePBS (enshrined Proposer-Builder Separation), the issue becomes more complex as various deadlines are interconnected.

Potuz suggests that CL optimization through precomputing signature verifications during slot dead time could help handle the load, emphasizing that attack costs should be proportional to computational burden. The discussion concludes with Potuz mentioning he has an idea to stop the attack and will document it, while also planning to work on Prysm optimizations and benchmarking. No final consensus is reached, leaving the optimal solution as an open question.

**Participants:** barnabasbusa, mariusvanderwijden, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T09:46:13.184000+00:00] potuz: I think the right thing to do is what I said originally to do: add a longer exit churn for builders: this makes the attack actually cost money at least
[2026-05-08T09:47:29.214000+00:00] potuz: Limiting at the EL seems not to be simple
[2026-05-08T09:47:59.202000+00:00] barnabasbusa: how are we limiting blob txs?
[2026-05-08T09:48:19.521000+00:00] potuz: Oh you mean statically count that type of txs
[2026-05-08T09:48:27.631000+00:00] barnabasbusa: yeah
[2026-05-08T09:48:29.035000+00:00] potuz: Yeah that would work
[2026-05-08T09:48:37.542000+00:00] barnabasbusa: would be the simplest imho
[2026-05-08T09:52:07.628000+00:00] potuz: Adding a new tx type is not trivial I think
[2026-05-08T09:55:04.906000+00:00] barnabasbusa: how would clients be able to 'ban' legacy deposit format?
[2026-05-08T09:56:56.193000+00:00] potuz: I think the best solution to this issue is if we're able to handle these deposits. Notice that in theory the CL *can* optimize to precompute these signature verifications before the next block arrives
[2026-05-08T09:57:08.215000+00:00] potuz: And only limit these by gas limit
[2026-05-08T09:57:45.419000+00:00] potuz: And anyway we need to make the exit churn larger to make this attack more expensive
[2026-05-08T10:04:57.791000+00:00] barnabasbusa: I don't think its reasonable to process 10k deposits in a single slot
[2026-05-08T10:05:18.434000+00:00] potuz: Cc <@363800010518822915> we probably want to change the proposer code to cache the requests as well... After all I was right that 5094 would make this more complicated lol
[2026-05-08T10:05:50.634000+00:00] potuz: It doesn't need to be reasonable, it needs to be expensive to justify the computation
[2026-05-08T10:06:20.633000+00:00] potuz: As with anything else, the cost of the attack needs to be comparable with the compute time taken
[2026-05-08T10:12:51.043000+00:00] potuz: <@360491619402776577> is there a way to make the gas cost of successive deposits in the block grow exponentially? That would be the most elegant way of fixing this IMO
[2026-05-08T10:13:23.882000+00:00] potuz: Does dynamic pricing allow for this?
[2026-05-08T10:14:03.566000+00:00] mariusvanderwijden: replace the deposit contract
[2026-05-08T10:14:10.696000+00:00] mariusvanderwijden: Its not really possible
[2026-05-08T10:14:24.113000+00:00] potuz: Hmm this is a no go from the start lol
[2026-05-08T10:14:54.085000+00:00] potuz: I'll work in optimizing this on Prysm and get a benchmark
[2026-05-08T10:15:02.729000+00:00] barnabasbusa: yeah I really don't think replacing the deposit contract is possible
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
[2026-05-08T10:19:37.238000+00:00] barnabasbusa: <@360491619402776577> how are you limiting number of blob txs ?
[2026-05-08T10:19:50.325000+00:00] barnabasbusa: and why can't we do the same for number of deposit txs?
[2026-05-08T10:20:03.546000+00:00] mariusvanderwijden: We know outside of the txs if there are blobs. We don't know if the transaction will deposit
[2026-05-08T10:20:16.207000+00:00] mariusvanderwijden: Deposit transaction type lfg
[2026-05-08T10:20:28.315000+00:00] potuz: Exactly, this would only work with a new tx type or similar
[2026-05-08T10:20:48.654000+00:00] potuz: Not reverting
[2026-05-08T10:21:12.421000+00:00] barnabasbusa: 
[2026-05-08T10:21:25.319000+00:00] barnabasbusa: yeah basically
[2026-05-08T10:21:37.177000+00:00] potuz: The precompute on the CL side is in the dead time of the slot
[2026-05-08T10:21:46.100000+00:00] potuz: It should be easy to handle
[2026-05-08T10:26:17.664000+00:00] potuz: Ah ok, I got an idea how to stop the attack. Will write it down
[2026-05-08T10:27:44.823000+00:00] barnabasbusa: https://klipy.com/gifs/stressed-out-suspense--k01KR3J1Z40WVZMH7AKD36T2QNQ
```

</details>

