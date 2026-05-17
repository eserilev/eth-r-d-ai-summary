# Forkchoice Bug and Checkpoint State Cache Issues

**Channel:** epbs | **Date:** 2026-04-07

## Summary

The discussion centers on two interconnected issues: a forkchoice bug and an API bug related to checkpoint state caching in Ethereum consensus clients. Potuz advocates for separating these issues, proposing to fix the forkchoice bug by removing the checkpoint state cache (which he considers an unnecessary implementation artifact) and using `get_ancestor` calls instead. Tuyen4818 proposes tracking finalized checkpoint payload status to prevent reorgs, but this approach faces strong opposition from sproul, who argues it would make finalization subjective and weaken crypto-economic guarantees by potentially allowing network splits without slashing.

The discussion reveals additional complexity around justified balances, where the ambiguity of payload presence in checkpoints could cause fork choice views to diverge between nodes. Various solutions are debated, including changing the Checkpoint type definition, though this raises concerns about handling slot 0 FFG votes and excluding some validator balance from finalization calculations. There's also discussion about API-level solutions for checkpoint sync that wouldn't require consensus changes.

By the end of the discussion, there appears to be growing consensus around Potuz's position that dramatic consensus rule changes shouldn't be made to address what are fundamentally API and implementation optimization issues. The participants acknowledge that the checkpoint state cache itself could be problematic for client implementations (with Prysm potentially having a bug related to this), but agree that the core issue should be addressed without modifying consensus-level definitions like the Checkpoint type.

## Participants

- 0xunclebill
- nflaig
- potuz
- sproul
- tbenr
- tuyen4818

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-07T00:39:54.166000+00:00] potuz: Can we break this problem in two? There's a forkchoice bug and an API bug. I won't make any statements about the API bug but I want to fix the forkchoice one. To fix the forkchoice one we do not need to change any checkpoint type and in fact we can just remove the whole checkpoint state cache that is an artifact that leaks unnecessarily into implementations. We should have calls to get_ancestor and if spec maintainers want to optimize this by having an internal cache that is not leaked into the markdown that's fine
[2026-04-07T03:07:48.207000+00:00] tuyen4818: https://github.com/ethereum/consensus-specs/pull/5073
the concern is about the payload of the skipped slot finalized checkpoint could be reorged
but that's also something we want to avoid, to ensure forkchoice to have as less reorg as possible
here I propose a candidate:
- whenever a checkpoint is promoted to be finalized, also pin its payload_status, tracked as a new field `finalized_checkpoint_payload_status` in Store
- reject blocks if it's NOT derived from that finalized checkpoint payload status

so it should be certain to know which checkpoint state to be used as `finalized` endpoint
please let me know your ideas cc <@755590043632140352> <@534934855113506836> <@586161934425128960> <@358120958726373381> <@602753420033785856> <@520034910149410861>
[2026-04-07T04:13:13.393000+00:00] sproul: I don't like this solution, because it changes the definition of finalized to be subjective and based on the node's local view. Even though it's unlikely to be divergent.
[2026-04-07T04:26:35.684000+00:00] tuyen4818: yeah that's a true concern
but first do you agree with that approach to not allow reorg the payload once a new finalized checkpoint is known?
we can get from finalized_checkpoint_payload_status from BeaconState instead I think, that should be safer
[2026-04-07T04:27:21.078000+00:00] sproul: No, I don't agree with that approach. The payload is not actually finalized unless we change the definition of Checkpoint to represent that
[2026-04-07T04:28:42.691000+00:00] sproul: If we adopt anything like this, then it's possible for two nodes to have different ideas of "finalization" _without_ anyone getting slashed. It weakens the whole crypto-economic guarantee of finalization. I'm worried that an attacker could try to exploit this to create a permanent network split that could only be resolved manually (by checkpoint syncing half the network to the "correct" side)
[2026-04-07T04:31:11.763000+00:00] sproul: <@688748669268132001> has also realised that this ambiguity about payload presence creates an issue with the justified balances too. The "justified checkpoint" is no longer sufficient to uniquely determine the justified balances, so fork choice views can diverge (a lot) between nodes that have/don't have the justified block's payload. E.g. imagine justified block in epoch 1 is justified at epoch 20 (skips between epoch 1 and 20). The payload from this block is capable of (greatly) influencing the balances of validators at epoch 20
[2026-04-07T04:44:12.854000+00:00] sproul: Issue for the justified balances issue here: <https://github.com/ethereum/consensus-specs/issues/5074>

With this I am leaning towards just copping the change to the Checkpoint type. It feels like the conceptually correct change to make, with less tech debt.
[2026-04-07T07:30:49.297000+00:00] nflaig: found the same issue yesterday, we discussed a few approaches internally but none seems to quite be right, see [summary](https://gist.github.com/lodekeeper/5da112623d62f9ee270130090a7d3f19).

> With this I am leaning towards just copping the change to the Checkpoint type
how would you deal with slot 0 FFG vote?
[2026-04-07T07:37:51.161000+00:00] sproul: Not sure how to deal with the slot 0 case, it's a bit terrible either way. We can't count it towards the full checkpoint, but also don't want to penalise those slot 0 attesters. Maybe they could be assessed for rewards based on their block_root + epoch only, but excluded from the FFG calculations :\
[2026-04-07T07:51:36.581000+00:00] 0xunclebill: so we'd lose 2 slots worth of validator balance from finalizaton. instead of 100% of validator balance, 15/16 of  total active validator balance would be voting to finalize?
[2026-04-07T07:54:48.523000+00:00] tbenr: maybe we could introduce a dedicated api, which just exposes finalized state for doing checkpoint sync, and leave the `/eth/v2/debug/beacon/states/finalized` unchanged. the new api will be equivalent to `/eth/v2/debug/beacon/states/finalized` in pre-gloas.
[2026-04-07T07:56:27.224000+00:00] nflaig: I don't think it's possible with the current spec, if you wanna advance the state to epoch boundary you need to apply the payload which is only justified, so the state you return as finalized, actually isn't
[2026-04-07T07:57:25.668000+00:00] tbenr: ye that's true. slot advancing is broken
[2026-04-07T10:48:11.106000+00:00] potuz: This seems to me that is getting out of hand over an API thing. The spec is rather clear that the justified/finalized state is the post CL state to the block with the checkpoint's root. There can't be any issues with justified balances nor any consensus split. There's an API problem with checkpoint sync that has some consumers and there's an issue with the python spec handling of a state cache. I do not see why we would go with dramatic systematic changes over this
[2026-04-07T10:58:23.657000+00:00] potuz: In short I strongly disagree with this: it doesn't make sense to change the consensus rules over what the checkpoint endpoint should return or over a Python optimization instead of using the attestation state for validation.
[2026-04-07T13:09:12.267000+00:00] sproul: That issue is invalid? Maybe we can flesh out exactly why on Github (I tagged you there)
[2026-04-07T13:10:44.272000+00:00] tbenr: yeah indeed, seems all good in teku
[2026-04-07T13:17:27.341000+00:00] potuz: I do not say the issue is invalid: just that the cache itself is a Python optimization to not call `get_ancestor`. The notion of justified/finalized state should be the post CL-state with the current checkpoint tracking, so there should not be any ambiguity as to which state is used. What I disagree is on changing consensus over this (eg. changing the notion  of Checkpoint) as this would be a dramatic change with unforseen problems over something that can be dealt offchain as is the checkpoint state cache in forkchoice
[2026-04-07T13:22:51.958000+00:00] sproul: I think the linked github issue is invalid tho
[2026-04-07T13:23:28.301000+00:00] sproul: and I am coming around to your position
[2026-04-07T13:27:59.042000+00:00] potuz: The checkpoint state cache indeed could be a problem though, <@688748669268132001> is right that it is a source of bugs if client implementers keep different checkpoints for attestation validation for example. I'm pretty sure that Prysm has this as a bug right now, so if we had a test with an entire epoch of missing blocks and the checkpoint payload including exits, I think we would produce the wrong validation.
```

</details>
