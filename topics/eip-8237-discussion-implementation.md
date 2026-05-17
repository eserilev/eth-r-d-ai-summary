# EIP-8237 Discussion and Implementation

---

## 2026-05-12 (epbs)

The discussion centers around potentially reviving EIP-8237, which aims to remove execution payloads from consensus layer (CL) sync by cutting out the largest part of beacon blocks. Nero_eth advocates for the proposal, noting it would be beneficial for higher gas limits and a future zkEVM world, while also mentioning that execution layer (EL) implementation appears relatively straightforward based on existing specs.

Implementation perspectives from client teams reveal mixed positions. Lighthouse (0xunclebill) indicates they can support the change and it would actually simplify their implementation, though they previously had concerns that it might prevent ELs from eventually removing their networking layers. Potuz clarifies that while the EIP doesn't explicitly document API method removals, the intention is to eliminate the need for downloading payloads by range from the CL. The group discusses scheduling further discussion for Thursday's call to get updates from various client teams.

Key open questions include whether the assumption about ELs wanting to remove networking layers is reasonable, and getting clarity on implementation perspectives from Nimbus, Teku, and Lodestar client teams. There's also uncertainty about the current scope of EIP-8237, specifically regarding whether it's been reduced to just adding an accumulator or still includes the broader payload serving changes.

**Participants:** 0xunclebill, barnabasbusa, nero_eth, potuz, terencechain, wemeetagain

<details>
<summary>Raw messages</summary>

```
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

---

## 2026-05-13 (epbs)

arnetheduck expressed concerns about EIP-8237, arguing it introduces technical debt on both consensus layer (CL) and execution layer (EL) sides. They noted that execution layer teams have shown surprising support for EIP-7807 (SSZ blocks), which they consider a superior approach that provides the same benefits as EIP-8237 without the associated technical debt. Specifically, EIP-7807 would allow removal of the EL-CL link in a cleaner way while achieving the same functionality.

Regarding Nimbus implementation, arnetheduck indicated that EIP-8237 is not necessary for independent syncing goals, as they can achieve the same result by importing RLP in the consensus layer and performing necessary checks there. This suggests an alternative implementation path that avoids the complexity introduced by EIP-8237.

**Participants:** arnetheduck

<details>
<summary>Raw messages</summary>

```
[2026-05-13T08:14:31.604000+00:00] arnetheduck: my general feeling about this approach is that it introduces technical debt as-is both on the cl and el side - from discussions with EL:s, they were surprisingly supportive of SSZ blocks (https://eips.ethereum.org/EIPS/eip-7807) which is superior because it provides the same benefit (and many others) as 8237 _without_ introducing said debt, ie 7807 similarly allows us to remove the EL-CL link, but in a clean way. 

nimbus does not need 8237 for gloas to sync indepdently, ie we achieve the same result by importing rlp in the the CL and performing the necessary check there
```

</details>

