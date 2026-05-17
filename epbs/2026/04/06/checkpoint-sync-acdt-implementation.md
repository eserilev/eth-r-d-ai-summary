# Checkpoint State Semantics and ACDT Discussion

**Channel:** epbs | **Date:** 2026-04-06

## Summary

The discussion centers on a technical disagreement about checkpoint state semantics in Ethereum's beacon chain, particularly affecting checkpoint sync functionality. nflaig raised concerns that a recent change breaks Lodestar's checkpoint sync on mainnet, as they expect the state to be advanced to the epoch boundary per the `store_target_checkpoint_state` specification. This issue was previously discussed in beacon-APIs issue #423.

potuz acknowledged merging the change but clarified that in Gloas (likely a fork upgrade), advancing the state is "strictly incorrect" and would require returning the post-payload state instead. This change would be subtle but significant for Prysm and could result in checkpoint servers returning states that honest nodes might reorg. The participants noted that there are still specification gaps regarding checkpoint state semantics for Gloas, with `store_target_checkpoint_state` being a phase0 concept that needs redefinition.

The discussion concluded without resolution, with potuz agreeing to write a comprehensive document outlining the issue within an hour to facilitate a proper technical discussion rather than continuing the ad-hoc chat. The core tension appears to be between maintaining backward compatibility with existing checkpoint sync implementations and adhering to new correctness requirements in upcoming protocol changes.

## Participants

- 0xunclebill
- nflaig
- potuz
- qu0b

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-06T11:22:49.361000+00:00] nflaig: is this intended for the devnet only? I'm pretty sure this breaks checkpoint sync with lodestar on mainnet as we expect the state to be advanced to the epoch boundary, also per `store_target_checkpoint_state` that seems right.
[2026-04-06T11:22:54.059000+00:00] nflaig: I also found previous discussion regarding this here https://github.com/ethereum/beacon-APIs/issues/423
[2026-04-06T11:25:56.094000+00:00] potuz: I merged it in develop, happy to discuss this today in ACDT but in Gloas advancing it is strictly incorrect. If clients want to advance it then we need to return the post Payload state. I don't mind this (although this will be a rather subtle change in Prysm) but technically this will make the checkpoint server return something that can technically be reorged by honest nodes
[2026-04-06T11:26:11.813000+00:00] potuz: Oh, there's no ACDT today?
[2026-04-06T11:26:13.754000+00:00] qu0b: we have a ACDT today?
[2026-04-06T11:26:19.390000+00:00] potuz: Jinx
[2026-04-06T11:26:27.550000+00:00] qu0b: https://tenor.com/view/rabbit-clock-clock-rabbit-rabbit-time-time-rabbit-bunny-clock-gif-13639927522938410597
[2026-04-06T11:26:33.105000+00:00] potuz: We should have a discussion and agree about this
[2026-04-06T11:26:43.912000+00:00] nflaig: in any case, aren't there gaps in the spec still regarding this for gloas?
[2026-04-06T11:27:17.751000+00:00] potuz: Yes, I left most of the API intentionally out of the scope I targeted for
[2026-04-06T11:27:35.777000+00:00] nflaig: no but I mean `store_target_checkpoint_state` is phase0
[2026-04-06T11:28:10.337000+00:00] nflaig: we need to redefine what a checkpoint state means
[2026-04-06T11:31:12.551000+00:00] 0xunclebill: it’s the block state
[2026-04-06T11:31:24.750000+00:00] 0xunclebill: that looks correct to me?
[2026-04-06T11:31:36.636000+00:00] potuz: not if it's required to be advanced
[2026-04-06T11:32:40.606000+00:00] potuz: I think we all understand the nuances, but setting it up in the spec is a little awkward, probably better to have a full writeup instead of dicussing here in the air. I'll write a full document on this issue, and have it ready in an hour or so
```

</details>
