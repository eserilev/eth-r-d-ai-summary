# Execution-merge Alternative to Variable PTC Deadline

**Channel:** epbs | **Date:** 2026-02-09

## Summary

anderselowsson explains the "execution-merge" concept that emerged as an alternative to variable PTC deadlines during discussions in Berlin. Under this approach, attesters would freeze their view of block execution capability around the slot boundary while continuing execution attempts until the attestation deadline. Attesters finishing execution before the freeze deadline would enforce block extension, those finishing between freeze and attestation deadlines would allow proposer discretion, and those unable to execute in time would vote against extension.

While execution-merge could offer advantages for future protocol changes like payload chunking (where variable deadlines might be difficult to implement), anderselowsson identifies critical flaws that make it unviable. The main issue is that powerful validators with fast execution capabilities could still vote against blocks that the majority cannot execute, since execution time varies by individual node rather than reflecting the actual gas usage that matters for network consensus. The approach would work better if based on payload arrival times and gas distribution rather than individual execution timing.

anderselowsson concludes that execution-merge appears unviable due to these limitations and the reduced information available to proposers for decision-making. They suggest instead considering making PTC votes more nuanced by accounting for execution gas in blocks, while questioning whether this gas accounting is already implemented in the current system.

## Participants

- anderselowsson

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-09T19:10:23.653000+00:00] anderselowsson: I suppose you refer to "execution-merge" <@520034910149410861>. Execution merge was an idea that came up in Berlin as we were discussing the issues that the variable PTC deadline is intended to address, before we knew about that idea. Attesters freeze their view of whether they could execute the block around the slot boundary. They still try to execute the block until the attestation deadline as normal. An attester that finishes executing the block before the freeze deadline enforces that the proposer must extend the current block, an attester that finished execution in the window between the freeze deadline and attestation deadline allows the proposer to decide, and an attester that does not have the time to execute the block always vote against extending on top of it.

An argument in favor of execution-merge is that future changes to the protocol may make a variable deadline difficult to get right. For example, under payload chunking, it may be rather tricky to design an enforceable variable deadline while upholding scaling instead of just checking by the end of the slot whether the chunks arrived in such a way that the full payload could be executed. 

However, the initial idea is not viable. A powerful validator may always have time to execute the payload, while the majority can't. It would still like to vote against a block extending on that payload. What matters is the amount of gas used for execution, not how long it took to execute for the individual node. Execution-merge would thus be better if attesters rely on payload arrival time(s) and execution gas distrbution in the chunks, according to some standard (potentially compensating for its connectivity). 

Another downside is that the proposer has less information to go on without the vote, and may make honest mistakes. Therefore, in conclusion, execution-merge seems unviable, but we might consider making the PTC vote a bit more nuanced by accounting for execution gas in the block. Or do we do that already?
```

</details>
