# Epoch Transitions and FCU Architecture Changes

**Channel:** epbs | **Date:** 2026-04-14

## Summary

The discussion centers on architectural challenges introduced by a PR (likely EIP-5094) that requires epoch transitions to occur **before** calling FCU (forkchoiceUpdated) when proposing blocks at epoch boundaries. Potuz identifies this as a significant architectural change that will necessitate complex NSC (Node State Cache) modifications they had hoped to avoid. Different clients handle this differently - Lodestar already runs state/epoch transitions before FCU calls, while other clients send many operations to background threads to avoid blocking the attestation hot path.

The participants acknowledge a key benefit of the changes: epoch transitions can now run much earlier in the previous slot since they no longer need to wait for payload arrival, which could previously leave only 3 seconds for the transition and force clients to run dual epoch transitions. However, Potuz highlights a fundamental shift in EIP-5094 where beacon states no longer distinguish between full or empty blocks, making the concept of "head state" less meaningful and requiring external information to determine block construction parameters.

The most consequential change identified is that nodes can no longer construct canonical blocks from beacon state alone - they now need offchain information to determine whether to build on full or empty blocks and require access to full requests when building on full blocks. While this doesn't appear to pose liveness risks, it represents a significant architectural departure from current block construction paradigms.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-14T18:27:15.466000+00:00] potuz: I think the biggest problem of your PR <@586161934425128960> as of now is that you need to do an epoch transition **before** you call FCU to propose next block at epoch boundary. It's not much of a problem, but this is an architerctural change for us
[2026-04-14T18:27:52.747000+00:00] potuz: there's no way to avoid this thus, we'll have to bite the bullet and do the complicated NSC changes that I was afraid of, cc <@363800010518822915>
[2026-04-14T18:29:40.921000+00:00] nflaig: that's true, however you can do that as soon as you have the beacon block, so timing wise seems fine, architecture wise it might be different based on client, we already run state/epoch transition before calling FCU in lodestar in pre-gloas
[2026-04-14T18:31:42.334000+00:00] potuz: we don't we send many of these things to the background to not lock on the hot path of attestation. We'll need to see what to do, probably we'll end up sending two different FCUs to the EL. one with and another without payload attributes
[2026-04-14T18:31:46.395000+00:00] nflaig: on the flip side, the benefit here of 5094 compared to before is that you can run the epoch transition much earlier into the previous slot since you not longer have to wait for the payload to arrive (which could be late depending on ptc deadline), let's say 9 seconds, then you only have 3 seconds to do the epoch transition, and likely clients would have to run 2 epoch transition just to be prepared
[2026-04-14T19:34:48.407000+00:00] potuz: I think a distilled form of the discussion in threads is that the most fundamental difference in the spec that 5094 brings is that a state doesn't discern between full or empty so the notion of "head state" loses some meaning. This is evidenced by the fact that we now need offchain information to construct a block. That is. 

"A node today is given a beacon state and it doesn't need anything else to construct a block that builds on it and is automatically canonical. "

This is no longer true with 5094 because the node needs to have external information to tell it if it's block needs to build on full or empty. And in case it needs to build on full it requires to have the full requests also available. 

I don't think this is a risk to liveness, but it is a consequential change
```

</details>
