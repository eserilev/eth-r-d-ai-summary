# Builder deposit and activation issues at fork

**Channel:** epbs | **Date:** 2026-01-27

## Summary

The discussion centers on a critical issue with builder activation timing during the Ethereum fork transition in PR #4817. Potuz identified that builders onboarded at the fork would have their deposit epoch set to the fork epoch, causing a delay of approximately 3 epochs before they become active and can submit bids. This creates a problematic gap where no builder bids would be included immediately after the fork.

A secondary bug was discovered related to the finalization constraint for builder activation. The current implementation uses the state's epoch as the deposit epoch, which could allow builders to be activated based on non-finalized blocks that might be reorganized. Potuz suggested either changing the deposit epoch for fork onboarding to be one epoch earlier or modifying the `is_active_builder` check to use `finalized_checkpoint.epoch - 1`.

The discussion concluded with jtraglia creating PR #4868 to address the timing issue, which should allow builders to be completely active at the fork if their deposits are finalized beforehand. The team also noted concerns about early deposits with 0x03 credentials being processed as validator deposits before the fork, which would require costly workarounds involving additional deposits to exit and re-deposit properly.

## Participants

- barnabasbusa
- bharath.vedartham
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T01:28:49.027000+00:00] potuz: Another issue that I do not like, on https://github.com/ethereum/consensus-specs/pull/4817 we onboard builders at the fork, but the deposit epoch is set to be the fork epoch, so those builders would not be active until two epochs later. We still haven't solved the issue of those builders participating. cc <@981249983196041218>
[2026-01-27T01:29:17.904000+00:00] potuz: No bids from those buidlers will be included, the happiest case would be 1 epoch after the fork
[2026-01-27T01:55:27.966000+00:00] potuz: <@592004585506340885> this is a double bug that wasn't caught yet. If we do want to enforce this activation after finalization constraint then `apply_deposit_for_builder` should not use the state's epoch as the deposit epoch. A deposit request at slot `32E + k` for   0 < k < 32 will be added with a deposit epoch of `E`. When the finalized checkpoint gets to have epoch `E` this builder's deposit will be considered finalized and the builder will be activated, when in fact the block 32E + k is still not final and can be reorged.
[2026-01-27T01:56:55.440000+00:00] potuz: We could change the deposit epoch in the fork onboarding to be one epoch before (since the deposits were in fact in the previous epoch) but the normal flow will probably need to change `is_active_builder` to check against `finalized_checkpoint.epoch - 1`
[2026-01-27T01:57:42.003000+00:00] potuz: We can probably ditch the finalization condition to being an active builder, I am not certain here... sorry to tag again <@520034910149410861>
[2026-01-27T02:09:49.903000+00:00] potuz: I will open an issue about this to have it documented somewhere so that I can link it in the annotated version
[2026-01-27T08:11:53.836000+00:00] barnabasbusa: is this really a problem? 90% of the people will still probably use mev boost and have trusted blocks built at and around the fork. Not having builders right around the fork doesn’t seem that big of a deal to me.
[2026-01-27T08:38:03.081000+00:00] potuz: How are they going to use MEV boost?
[2026-01-27T08:39:01.383000+00:00] barnabasbusa: Are all clients gonna disable mev boost at the fork?
[2026-01-27T08:39:24.147000+00:00] barnabasbusa: I thought that mev boost will live on and people can opt in or out to use it
[2026-01-27T08:39:37.590000+00:00] barnabasbusa: and maybe axe mev boost in heze
[2026-01-27T08:54:23.814000+00:00] potuz: Prysm will not support blind signing, there isn't even a blind object specified. I am unaware of any attempts by MEV boost or commit boost on trying to have a middleware to deal with blind signing. Other clients can implement this, I am unaware of clients trying to do this.
[2026-01-27T13:33:47.063000+00:00] potuz: nevermind this, I didn't see that the check is for < and not for <=
[2026-01-27T14:11:02.675000+00:00] potuz: It's going to be approx 3 epochs after the fork, we should probably do something to change the ones that are onboarded at the fork so that at least this is reduced to just one epoch instead of 3 https://github.com/ethereum/consensus-specs/issues/4867
[2026-01-27T15:02:44.760000+00:00] jtraglia: This was a good catch & easy to fix. Made this PR: https://github.com/ethereum/consensus-specs/pull/4868
[2026-01-27T15:06:17.682000+00:00] jtraglia: Btw with this change, I think builders can be completely active at the fork.
[2026-01-27T15:06:52.710000+00:00] jtraglia: The builder's deposit (from the pending deposit) could be finalized before the fork.
[2026-01-27T15:32:22.449000+00:00] bharath.vedartham: if a builder submits a deposit with a 0x03 credential before gloas but it gets processed into a validator deposit before the fork, the only thing the builder can do is to exit the validator and redeposit post the fork right?
[2026-01-27T15:42:01.439000+00:00] jtraglia: If they deposit too early, it'll be annoying to get that deposit back. To perform an exit, the validator has to be active & it cannot be active without the minimum activation balance (32 ETH).
[2026-01-27T15:42:25.773000+00:00] jtraglia: 
[2026-01-27T15:43:58.312000+00:00] jtraglia: So they'd probably need to deposit an additional ~30 ETH in order to exit.
[2026-01-27T15:44:36.948000+00:00] jtraglia: Which is why I made: https://jtraglia.github.io/onboard-builders/
[2026-01-27T15:52:50.356000+00:00] potuz: looks good to me, will review later tonight
```

</details>
