# Prysm serving advanced states for skipped slots

**Channel:** epbs | **Date:** 2026-04-05

## Summary

The discussion centers on an issue with Prysm serving advanced states for skipped slots during checkpoint sync. 0xunclebill reported that Prysm is providing finalized checkpoint states that have been advanced to the checkpoint slot (e.g., slot 35200) even when that slot was skipped, rather than serving the expected post-block state from the previous non-skipped slot (35199). This behavior complicates checkpoint synchronization for client implementations.

Tuyen4818 proposed a solution involving a new `get_finalized_state()` API that would use `justified_checkpoint` + `get_ancestor()` to determine the correct state variant (post-block or post-envelope) and advance it appropriately to maintain epoch alignment. However, 0xunclebill suggested that always using post-block states might be simpler for Lighthouse, and requested that checkpointz support serving envelopes via `GET eth/v1/beacon/payload_envelopes/{block_id}` to reduce complexity.

Potuz clarified that if confirmed, Prysm's behavior represents a bug, as finalized states don't need to be advanced to the finalized epoch and should always be the post-CL block with the finalized root, without envelopes regardless of their presence. He questioned what specific problems implementers are facing with checkpoint sync, noting that the standard approach involves requesting blocks and payloads by range and deciding whether to apply the first payload. The issue requires further investigation, with Potuz offering to study it after the weekend.

## Participants

- 0xunclebill
- potuz
- tuyen4818

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-05T09:24:04.569000+00:00] 0xunclebill: I think prysm is serving postBlock states that are advanced to the checkpoint slot, even when the checkpoint slot was a missed slot
[2026-04-05T09:24:35.731000+00:00] 0xunclebill: i would assume that I would just receive the un-advanced postBlock state
[2026-04-05T09:39:25.500000+00:00] 0xunclebill: yeah for example prysm is serving me the finalized checkpoint state for slot 35200
[2026-04-05T09:40:08.251000+00:00] 0xunclebill: 35200 is a skipped slot, id expect to receive the post block state for slot 35199 instead,
[2026-04-05T12:28:45.057000+00:00] tuyen4818: using the post-block state as the finalized state is safe but not ideal, I think this is the key issue of the complexity of checkpoint sync for skipped slot checkpoint for now

I propose a way to compute the finalized state of skipped slot checkpoint, this should be safe enough https://hackmd.io/@JVtpwRK3SwmkRIFfF0Bmyg/SJBpk6yhZe
TLDR:
- we don't know payload status of the previous block (that's later become finalized's check point root) at the time passing through it, also in the FFG vote
- but we can use `justified_checkpoint` + `get_ancestor()` at any time, to know the exact variant (EMPTY or FULL) of the finalized checkpoint. Note that all heads should be derived from the `justified_checkpoint` for now.
- then use respective state (either post-block or post-envelope) of the block root, and dial to checkpoint's epoch as the finalized state
- this ensures that the finalized state always has the same 1st slot to checkpoint's epoch, this is backward compatible
- maybe we can go with a new `get_finalized_state()` api in the spec?

this should help us do checkpoint sync easier because it's just the same to previous forks
cc <@358120958726373381> <@586161934425128960>
[2026-04-05T12:30:08.973000+00:00] tuyen4818: let me know your ideas on this <@755590043632140352> <@363800010518822915> <@602753420033785856>
[2026-04-05T13:06:14.378000+00:00] 0xunclebill: We have a PR in LH that supports a misaligned checkpoint state for the initial state only. subsequent states still keep the invariant that they must be epoch aligned.

so for us always using the post block state might be the simplest option. would be nice for checkpointz to enable serving the envelope via `GET eth/v1/beacon/payload_envelopes/{block_id}` . that would be the biggest reduction of complexity for us.

im not sure that having to deal with both state variants in checkpoint sync simplifies things for us, could be wrong about that though. happy to hear what others think
[2026-04-05T18:56:49.594000+00:00] potuz: If we do that that's a bug
[2026-04-05T18:58:05.058000+00:00] potuz: Just catching up on this channel, which screwed up country makes people work the day kids are hunting for saturated fat in the form of eggs
[2026-04-05T18:59:57.175000+00:00] potuz: Cc <@1057691141237125190> if this is confirmed that's a bug in that endpoint.  Finalized does not need to be advanced to the finalized epoch
[2026-04-05T19:00:28.807000+00:00] potuz: If this is blocking you <@358120958726373381> I can probably study this today but would rather do this Monday
[2026-04-05T19:02:08.471000+00:00] potuz: It should always be the post CL block with the finalized root. No envelope regardless of presence of the envelope on that branch. Any other design moves complexity from the server to the caller
[2026-04-05T19:03:31.776000+00:00] potuz: I fail to understand what is the problem with checkpoint sync that you guys are having. When you get a state you request blocks and Payloads by range and then decide if you need to apply the first Payload or not. What's the issue?
```

</details>
