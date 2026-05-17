# Queue Management and Builder Economics

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on a queue management vulnerability where established builders like Titan could cheaply fill up a queue to prevent new builders from entering the market. Potuz suggests this creates a barrier to entry problem, while m.kalinin proposes shuffling the queue at fork transitions as a potential mitigation.

The conversation reveals that there was previous debate about requiring 32 ETH as a barrier, with Potuz apparently advocating for this higher amount. The main opposition was that 32 ETH would price out smaller builders, though tersec questions how small builders can be while remaining well-financed enough to be useful. nflaig argues that 32 ETH only makes attacks more expensive without preventing them, especially for well-funded attackers like nation states.

Potuz clarifies that the attack wouldn't stall the chain but could cause beacon block reorgs, noting that similar liveness failures could be achieved more cheaply by buying payload slots and not delivering. While acknowledging that Misha's PR appears to work as a solution, Potuz expresses concern about making these changes now and suggests it would be preferable to handle such issues through gas limit mechanisms only.

## Participants

- 0xunclebill
- m.kalinin
- nflaig
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T11:43:12.031000+00:00] potuz: As for the number of builders, I do not really know the answer, but the problem I can see is that it is very cheap for say Titan or the usual builders that will win the first blocks, to prevent others from entering the game
[2026-05-08T11:43:27.180000+00:00] potuz: it may be very profitable for them to fill the queue
[2026-05-08T11:44:33.784000+00:00] m.kalinin: if this is a problem then I would shuffle the queue at the fork transition
[2026-05-08T11:44:43.171000+00:00] potuz: lol
[2026-05-08T11:45:31.984000+00:00] potuz: life would be much easier if we had requested 32 ETH  to be honest
[2026-05-08T11:47:42.254000+00:00] m.kalinin: i believe you tried to argue for that hard, didn't you? what was the main opposition?
[2026-05-08T11:49:41.185000+00:00] 0xunclebill: i think the argument was that 32eth prices out the smaller builders
[2026-05-08T11:58:25.799000+00:00] tersec: how small can a builder be an be well-enough-financed to be useful?
[2026-05-08T11:59:51.658000+00:00] nflaig: I don't understand how making it 32ETH solves anything, it just makes the attack more expensive, not impossible, still cheap for a nation state or similar
[2026-05-08T12:05:58.261000+00:00] potuz: the attack is not that problematic, is not that it stalls the chain, it just may reorg some beacon blocks. You can achieve that sort of liveness failure cheaper by simply buying the payload slot and not delivering. By making this more expensive we detract attackers from even exercising this
[2026-05-08T12:06:54.551000+00:00] potuz: At any rate, Misha's PR *seems* to work, but I still think it' s a bad idea to start making these changes now, it would be much nicer if we could handle these with the gas limit only
```

</details>
