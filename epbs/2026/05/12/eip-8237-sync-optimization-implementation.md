# EIP-8237 sync optimization discussion and implementation

**Channel:** epbs | **Date:** 2026-05-12

## Summary

The discussion centers around EIP-8237, a sync optimization proposal that would remove execution payloads from consensus layer (CL) sync by adding an accumulator structure. Potuz expressed strong regret about not pushing harder for EIP-8237's inclusion with EIP-5094, noting that most devnet problems stem from peering and syncing complexity. The EIP would allow the CL to avoid downloading payloads by range, instead letting execution layer (EL) clients handle payload sync directly, which becomes increasingly important as execution payloads grow with higher gas limits.

Implementation perspectives varied among client teams. Lighthouse indicated they would actually prefer this approach as it simplifies their architecture, contrary to earlier concerns about EL networking layer dependencies. The EL implementation appears relatively straightforward based on existing specs. However, there's uncertainty about whether ELs actually want to remove their networking layers in the future, with some viewing this as potentially naive assumption.

The discussion concluded with a proposal to revisit EIP-8237 on Thursday's call, seeking updates from Lighthouse and Nimbus on implementation complexity, while also needing broader EL client input. A key clarification emerged that while the EIP doesn't explicitly remove CL payload serving APIs, the intent is to eliminate the need for CL clients to download payloads by range.

## Participants

- 0xunclebill
- barnabasbusa
- nero_eth
- potuz
- terencechain
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-12T13:53:48.502000+00:00] potuz: if it were a unique call then it would be simpler... every single day since Svalbard I regret not fighting stronger for 8237
[2026-05-12T13:54:07.084000+00:00] potuz: essentially ALL problems we see on devnets steer from peering and boil down to syncing complexity
[2026-05-12T13:54:31.441000+00:00] potuz: it was utterly stupid not to bag 8237 with 5094
[2026-05-12T14:30:15.831000+00:00] barnabasbusa: ~~epbs~~ 8237 solves this
[2026-05-12T14:30:52.282000+00:00] barnabasbusa: I'm not sure how I feel about potentially bringing back 8237 into discussion now lol
[2026-05-12T15:13:19.339000+00:00] nero_eth: I think we should. It’s a great idea to cut the largest part of beacon blocks, the execution payload, which will only grow with higher gas limits, out of CL sync. At the same time it's beneficial for a post-zkEVM world and might be needed anyway in the future.
[2026-05-12T15:17:23.642000+00:00] nero_eth: Can we discuss 8237 again on Thursday's call?
I remember Lighthouse mentioned that it's not super simple to implement for them - would be great to get an update on that. Same applies for Nimbus. For Teku and Lodestar I forgot what they thought about it.
cc <@&1417820113981145228>
[2026-05-12T15:21:11.681000+00:00] terencechain: we also need EL right?
[2026-05-12T15:26:30.458000+00:00] nero_eth: On the EL it's a rather uncomplicated change, at least judging from the [specs-side](https://github.com/ethereum/execution-specs/pull/2770/changes/0710d41783c2a3aeed10077d2540ca9888a3f294), but yeah, would def need discussion too.
[2026-05-12T16:39:32.400000+00:00] wemeetagain: re 8237 discussion, I recall some mention of removal of CL serving of payloads? but I don't see that in the EIP.
Was the EIP scope reduced to just the addition of the accumulator?
[2026-05-12T18:11:29.634000+00:00] potuz: no, I never removed explicitly the API methods cause I didn't want to write up the API PR
[2026-05-12T18:12:03.758000+00:00] potuz: but that is the obvious intention of the EIP, I don't really care at all about that EIP if it doesn't come with the assumption that I do not need to ever again download payloads by range
[2026-05-12T19:37:42.974000+00:00] 0xunclebill: LH doesn’t have a problem supporting this. It actually makes our life easier. we were just convinced that ELs want to get rid of their networking layer eventually and this would prevent that from happening
[2026-05-12T20:09:04.520000+00:00] barnabasbusa: is this really reasonable assumption?
[2026-05-12T20:32:08.089000+00:00] 0xunclebill: lol maybe im naive
[2026-05-12T22:37:16.914000+00:00] nero_eth: Right now, and in the near future, I don't see why ELs would want that, esp not for sync where ELs are just about to ship a new snap sync version.
```

</details>
