# Withdrawals and deposits impact on devnet stability

**Channel:** epbs | **Date:** 2026-03-03

## Summary

The discussion centers on stability issues in devnet 0 caused by withdrawals and deposits. Potuz strongly advises against sending withdrawals as they could "completely destroy" the devnet, and confirms that deposits were already agreed to be excluded as they would also break the network. The core technical issue appears to be that withdrawal sweeps may kill the chain due to incorrect payload attributes being sent during block reorganizations.

Potuz identifies a specific edge case in Prysm where last-second reorgs generate bad payload attributes, particularly when Prysm decides not to reorg a late block due to attestations. This creates complex scenarios involving blocks with/without withdrawals and corresponding payloads that may or may not satisfy previous block withdrawals. Four test combinations are suggested to cover these scenarios, though Potuz suspects they already pass but believes wrong payload attributes are being sent during reorg situations.

The discussion concludes with barnabasbusa suggesting defaulting to 0x00 or 0x02 credentials to avoid triggering partial withdrawals, and jokingly proposing to migrate everyone back to 0x00 credentials. The withdrawal payload attributes issue during reorgs remains an open technical problem that Potuz is working to fix, describing it as "highly non-trivial."

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-03T13:31:31.276000+00:00] potuz: Please DO NOT send withdrawals in devnet 0, we'll be completely destroyed 🙂
[2026-03-03T13:35:56.406000+00:00] potuz: cc <@592004585506340885> if we don't have tests for this it'd be nice: block comes with withdrawals, payload does not come, next block comes with/without withdrawals and next payload comes satisfying/not satisfying the previous block's withdrawals. All 4 combinations are useful. 

I think we pass those, but I also think we're sending the wrong payload attributes if we need to propose in that situation reorging a payload
[2026-03-03T13:44:49.844000+00:00] barnabasbusa: I would assume same for deposits?
[2026-03-03T13:45:12.309000+00:00] barnabasbusa: I guess we should default to 0x00 or 0x02 creds then? If we don't wanna trigger partial withdrawals?
[2026-03-03T14:30:05.429000+00:00] potuz: deposits for sure will break people and we agreed not to have them
[2026-03-03T14:30:28.571000+00:00] potuz: problem is actually that I suspect just the withdrawal sweep may kill the chain on bad payload attributes
[2026-03-03T14:31:15.782000+00:00] potuz: a last second reorg gives bad Payload attributes currently at Prysm and that was probably what you saw: that Prysm was about to reorg a late block and decided not to do it in the very last second because of attestations, I am fixing that edge case, and it's highly non-trivial because of withdrawals
[2026-03-03T15:02:34.983000+00:00] barnabasbusa: Time to force migrate everyone back to 0x00 😂
[2026-03-03T15:02:53.222000+00:00] barnabasbusa: this is how epbs fixes ETH sell pressure
```

</details>
