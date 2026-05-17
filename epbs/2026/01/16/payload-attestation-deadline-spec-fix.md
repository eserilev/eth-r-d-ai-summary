# Variable PTC deadline for payload availability

**Channel:** epbs | **Date:** 2026-01-16

## Summary

The discussion centers on a proposed variable PTC (Payload Timeliness Committee) deadline mechanism to address potential attack vectors in Ethereum's block production timing. Fradamt explains that currently payload propagation and execution share overlapping time windows, creating vulnerability where builders could publish small payloads with heavy execution requirements late in the process, causing the next proposer to miss execution deadlines and trigger reorgs. The proposed solution would set variable deadlines based on payload size - smaller payloads must arrive earlier to allow more execution time, while larger payloads can arrive later since less gas would be available for execution anyway.

Potuz argues this complexity may be unnecessary with current slot times, suggesting the issue only becomes relevant with shorter slots. He proposes waiting for testing to determine optimal propagation and attestation deadlines before adding spec complexity, believing a 4-second payload availability deadline would prevent blocks requiring more than 8 seconds execution time even with a 10x gas limit increase. However, cperezz.eth pushes back on this assumption, citing that some clients like Reth already take over 3 seconds to process certain blocks due to state root recomputation and IO bottlenecks, making 8+ second execution times plausible with higher gas limits.

The discussion remains unresolved, with the core tension being whether to implement the variable deadline system proactively or wait for empirical testing data. Fradamt has prepared a spec change proposal, but no consensus was reached on timing for implementation.

## Participants

- cperezz.eth
- fradamt
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-16T10:57:47.338000+00:00] fradamt: The payload propagation and execution largely share the same time window, except for the payload having a little bit of extra execution time after the ptc deadline. Due to this, it has been previously suggested that we should have a variable ptc deadline for payload availability, for example based on the payload size: smaller payloads have to arrive earlier, leaving more time for execution, larger payloads can arrive later since there will be less gas available for execution anyway 

Some discussions about this have taken place this week and quite a few people are convinced that something along these lines is necessary to actually get the scaling benefits without introducing attack vectors. For example, in the current state a builder could publish a small payload with a lot of execution very late, around the ptc deadline, which would prevent the next proposer from executing it on time and end up in a reorg. 

Other options to mitigate this problem have been considered but ultimately we feel that the conceptually correct thing to do is to be able to express that the time we have is split between two different resources that have to be used sequentially, and can be used in different proportions. 

Here's a proposed spec change for it: https://github.com/ethereum/consensus-specs/compare/master...fradamt:consensus-specs:variable-ptc-deadline. In principle it should just be adding a note to the validator spec, however imo it would be nice to have an explicit computation for the payload attestation to have more clarity on how it should be done, so I introduced that (and a few minor downstream changes)
[2026-01-16T13:18:23.325000+00:00] potuz: I think this only becomes relevant on shorter slot times. With dual deadlines for PTC this attack is completely impossible with reasonable deadlines. I propose we do not increase spec complexity right now over this (since it's easy to add at any time as it's not a hard fork) and just wait until testing determines what is the propagation max and the attestation deadline we can get. I suspect that we can set the payload availability at 4 seconds and it is impossible to produce a block that takes more than 8 seconds to execute even if we were to 10 fold the gas limit.
[2026-01-16T13:51:11.658000+00:00] jtraglia: Variable PTC deadline for payloads
[2026-01-16T15:02:46.933000+00:00] potuz: <@520034910149410861> can you open a PR so that it's reviewed instead? This seems like a typo 
```
| `MIN_PAYLOAD_DUE_BPS`         | `uint64(3000)` | basis points | 30% of `SLOT_DURATION_MS` |
```

Ah nvm I misread the numbers
[2026-01-16T15:03:20.618000+00:00] cperezz.eth: We can definitely create a block that takes 8s to execute if we 10fold the gas limit. At least for some clients.

Ofc, with BALs and ePBS we don't know yet. But as of now, I def think there are ways to do so. 

Notice a simple thing: It takes Reth >3s sometimes to process XEN blocks. And the issue is state root recomputation taking too much IO time. 

We still need client optimizations in place to see how it will evolve. But as of today, I would not make the assumption: **and it is impossible to produce a block that takes more than 8 seconds to execute even if we were to 10 fold the gas limit**
```

</details>
