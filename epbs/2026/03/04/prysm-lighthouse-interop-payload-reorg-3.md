# Prysm Node Restart and Sync Testing

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The team conducted a restart and sync test of a Prysm-geth-1 node to investigate previous issues. After parithosh restarted the node, potuz observed improved connectivity with 3 peers and successful payload retrieval up to slot 255, noting that slot 281 was previously problematic. The node was receiving payloads properly, leading to speculation that it may have been banned by other peers in earlier attempts.

However, the sync eventually failed due to a block processing error around slots 288-321, with logs showing "could not get block's prestate" and missing parent block issues. Potuz identified this as a legitimate bug in Prysm rather than a network connectivity issue, clarifying that Prysm wasn't being banned but was instead banning other peers. 

Terencechain confirmed this was the same bug previously discussed in their debugging channel. The issue remains unresolved, with potuz planning to investigate further the following day after working on the problem for over 14 hours. The sync failure appears to be related to state reconstruction problems during the initial sync process.

## Participants

- parithosh
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T21:02:06.120000+00:00] potuz: <@199561711278227457> can you nuke again and reload Prysm-geth-1 just to try, same image that is there
[2026-03-04T21:02:07.817000+00:00] potuz: or probably I can
[2026-03-04T21:03:42.495000+00:00] potuz: let's restart prysm-geth and lets see what fails
[2026-03-04T21:07:35.414000+00:00] parithosh: on it
[2026-03-04T21:09:00.044000+00:00] parithosh: done
[2026-03-04T21:09:20.950000+00:00] potuz: looking now
[2026-03-04T21:09:53.780000+00:00] potuz: we now have 3 peers
[2026-03-04T21:10:02.306000+00:00] potuz: we got 64 payloads!
[2026-03-04T21:10:21.389000+00:00] potuz: I got to slot 255 already
[2026-03-04T21:10:41.102000+00:00] potuz: 281 is the problemaitc one
[2026-03-04T21:11:15.375000+00:00] potuz: we are getting payloads
[2026-03-04T21:11:24.121000+00:00] potuz: perhaps we were banned before?
[2026-03-04T21:12:36.393000+00:00] potuz: Ahh crap, that's our bug <@363800010518822915> [2026-03-04 21:12:07.05]  WARN initial-sync: Block processing failure error=could not get block's prestate: could not reconstruct parent state firstSlot=288 lastSlot=321 root=0xb319327d39615f9cb2edc2848da7f0d99c05499805dc608262f6c999d8224c1a
[2026-03-04 21:12:07.05] DEBUG initial-sync: Could not process batch blocks due to missing parent firstSlot=353 lastSlot=353 missingParent=0x62754303339efc7bfa8979135cc721514b208c2fe69939df298ac6df02e3a4c0 root=0x1c9024c97d394e3ea607fe841f7f92d69096771da13c2d89b4fd479c31a02c9a
[2026-03-04T21:12:39.965000+00:00] potuz: same slot as you?
[2026-03-04T21:12:46.948000+00:00] parithosh: what changed since the last resync? prysm didnt change so whatever banned it before could have banned it now too no?
[2026-03-04T21:13:01.168000+00:00] potuz: prysm wasn't being banned
[2026-03-04T21:13:04.729000+00:00] potuz: we banned everyone else
[2026-03-04T21:13:21.167000+00:00] potuz: but now this looks like an actual bug
[2026-03-04T21:14:06.688000+00:00] potuz: will leave that up to Terence, stopping about here
[2026-03-04T21:44:08.171000+00:00] terencechain: Ya that's the same bug I shared in our debugging chanel 🙂
[2026-03-04T21:48:59.323000+00:00] potuz: Thanks, if it's still unsolved I'll look at it tomorrow trying to sync, but today I've already been sitting at this desktop without leaving for over 14 hours
```

</details>
