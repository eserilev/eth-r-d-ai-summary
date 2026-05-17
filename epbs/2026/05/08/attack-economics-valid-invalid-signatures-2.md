# Builder Frontrunning and Attack Economics

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on builder frontrunning attacks and the economics of different attack vectors. m.kalinin argues that attackers wanting to frontrun others would pay for bid space and frontrun directly rather than using DoS attacks with deposits, and that this same frontrunning capability would exist even with FOCIL within the same block. potuz agrees with this assessment, noting that while DoS attacks seem inefficient, wealthy attackers might still pursue suboptimal strategies.

The conversation shifts to analyzing queueing mechanisms for deposit processing. m.kalinin advocates for queueing as a protection against frontrunning of smaller builders, while potuz expresses concern about late spec changes and the tradeoff between capping at epoch boundaries versus handling more deposits per slot. potuz argues that epoch-boundary capping makes the common case worse by batching deposits that could otherwise be processed gradually.

A key attack vector emerges where large builders could exploit queueing delays by creating many new builders (e.g., 1,600 builders) to delay competitor activation by extended periods (100+ epochs), with their only cost being interest rates over weeks. Both participants agree this could be highly profitable for attackers, though potuz suggests the worst-case scenarios should be technically manageable to implement.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T16:28:32.781000+00:00] m.kalinin: <@755590043632140352> once again thinking on the builder frontrunning others. As you mentioned if you want to make a slot empty, you buy a block space and don't reveal a payload rather than DoSing with deposits. So is with this case, if you want to frontrun, you pay for a bid and frontrun. Even with FOCIL, you will be able to frontrun within the same block
[2026-05-08T16:29:59.483000+00:00] potuz: yes, this seems correct to me
[2026-05-08T16:30:14.114000+00:00] potuz: dossing the chain with this attack seems silly to me
[2026-05-08T16:30:30.247000+00:00] potuz: but silly attackers with lots of money are not lacking 🙂
[2026-05-08T16:31:16.361000+00:00] m.kalinin: my argument is in favor of queueing as not queueing doesn't prevent frontrun of small builders
[2026-05-08T16:33:58.030000+00:00] potuz: I do not mind queueing (except the fact that it is a change in the spec rather late, it seems to be safe, but we will need to analyze and have new testvectors). But the point remains that capping at the epoch boundary seems silly as well, we would need to handle many more deposits than 16 IMO, and this pushes the happy path from "some per slot" to "several at epoch transition", making the happy case worse
[2026-05-08T16:36:44.578000+00:00] m.kalinin: Oh, I see. So, you want to prevent a *delay* that is introduced by the queue and can be abused by large builders. Like they can create 1,600 new builders and delay everyone else activation by 100 epochs?
[2026-05-08T16:37:30.548000+00:00] potuz: yes, this can be hugely profitable for builders
[2026-05-08T16:37:42.701000+00:00] potuz: specially if their cost is only interest rate over a few weeks
[2026-05-08T16:38:23.019000+00:00] potuz: but also I think it should be easy to handle for us the worst case
```

</details>
