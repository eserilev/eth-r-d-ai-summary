# Node Deployment and Chain Reorg Analysis

**Channel:** epbs | **Date:** 2026-03-09

## Summary

The team investigated node deployment issues and chain reorganizations on the epbs-devnet-0 testnet. When pk910 rolled the lodestar-geth-1 node, it unexpectedly started a full resync from genesis rather than continuing from its previous state. This was attributed to the previous version not archiving states properly, though nflaig noted the sync was proceeding smoothly despite starting over.

Multiple chain reorgs occurred during the session, with Prysm forking off at slot 38601 and initially building its own chain before other clients eventually switched to follow the Prysm chain as canonical. The team observed that these forks coincided with Lodestar node restarts, suggesting the restarts may have triggered the reorg events. There was also confusion about the block explorer UI, where potuz initially misunderstood the committee index display versus the payload status badges for distinguishing between full and empty blocks.

The team successfully deployed checkpoint sync on multiple nodes as a solution, with node2 and the third Lodestar node both syncing smoothly from checkpoints rather than genesis. Notably, the third node restart did not trigger another fork, suggesting the earlier correlation may have been coincidental. No code changes were made since the previous week, leaving the root cause of the initial reorg events still under investigation.

## Participants

- nflaig
- pk910
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T23:09:58.583000+00:00] pk910: I've rolled lodestar-geth-1, but it looks like the node started a full resync from genesis.  is that expected?
`INFO [03-09|23:06:59.210] Truncating from head                     type=state ohead=34710 tail=0 nhead=2`
[2026-03-09T23:16:51.048000+00:00] potuz: Huh? What's that full or empty tag? The committee index still says 0
[2026-03-09T23:16:56.232000+00:00] pk910: prysm forked off at slot 38601.
it first tried to reorg that block out, now building it'S own chain on it
[2026-03-09T23:18:20.227000+00:00] potuz: Really? Cool what changed?
[2026-03-09T23:18:42.268000+00:00] pk910: it looks like the prysm chain got the canonical chain now..  other clients switched over
https://dora.epbs-devnet-0.ethpandaops.io/slots?s=38635&c=50
[2026-03-09T23:20:40.275000+00:00] potuz: I'll try to catch logs tomorrow. Already trying to get kids to finish dinner and go to bed
[2026-03-09T23:30:58.328000+00:00] potuz: so what' s the Full green tag in that block above?
[2026-03-09T23:32:43.354000+00:00] pk910: full green = both block & payload present & canonical.

left side = beacon block status  (green = canonical, blue = orphaned, yellow = missed)
right side = execution payload status (green = canonical, blue = orphaned, yellow = missed)
[2026-03-09T23:33:14.594000+00:00] potuz: I mean in the attestation
[2026-03-09T23:33:19.589000+00:00] potuz: the committee is 0
[2026-03-09T23:33:37.341000+00:00] nflaig: I think the previous version didn't archive states properly so we started from genesis but sync looks smooth so far
[2026-03-09T23:33:43.718000+00:00] nflaig: thanks for deploying
[2026-03-09T23:34:12.771000+00:00] nflaig: committee index should be 0 I would assume, there is only one committee
[2026-03-09T23:34:29.604000+00:00] pk910: which block are you looking at? the link you replied for is the whole block list
[2026-03-09T23:35:08.955000+00:00] nflaig: <@808969530608451584> can we try restarting one node with checkoint sync
[2026-03-09T23:35:23.989000+00:00] pk910: yea,  I can roll node2 with checkpoint sync 🙂
[2026-03-09T23:35:52.643000+00:00] potuz: https://dora.epbs-devnet-0.ethpandaops.io/slot/38063
[2026-03-09T23:36:06.878000+00:00] potuz: This block, perhaps I missunderstood <@586161934425128960>' s comment
[2026-03-09T23:36:12.319000+00:00] potuz: but all attestations there are for empty
[2026-03-09T23:36:24.315000+00:00] pk910: no, the third is for full
[2026-03-09T23:36:36.177000+00:00] terencechain: 
[2026-03-09T23:36:43.226000+00:00] nflaig: yeah  also pulled that block locally, the third has index=1
[2026-03-09T23:36:47.559000+00:00] potuz: where does this come from? the index is 0
[2026-03-09T23:36:50.792000+00:00] potuz: in that same pane
[2026-03-09T23:37:00.673000+00:00] terencechain: i think the index displayed is worng?
[2026-03-09T23:37:07.742000+00:00] potuz: ok, that' s my question
[2026-03-09T23:37:14.037000+00:00] pk910: no.  that index you'Re looking at  is the commitee bitmask.  there would be more indexes if the bitmask is longer than 1 bit
[2026-03-09T23:37:14.310000+00:00] potuz: those two things are contradictory'
[2026-03-09T23:37:30.306000+00:00] potuz: oh
[2026-03-09T23:37:37.897000+00:00] potuz: crap that is incredibly confusing
[2026-03-09T23:37:49.868000+00:00] pk910: yeaa,   in such a small devnet it is indeed 😄
[2026-03-09T23:38:02.273000+00:00] potuz: ok, so all that matters is the payload status badge, thanks
[2026-03-09T23:38:16.120000+00:00] potuz: now on the other thing, why do you say Prysm forked off?
[2026-03-09T23:38:32.425000+00:00] potuz: in the fork you mention it looks like Lodestar tried to reorg Prysm
[2026-03-09T23:39:26.166000+00:00] potuz: And PRysm geth correctly reorgs the payload of prysm in https://dora.epbs-devnet-0.ethpandaops.io/slot/38601 because everyone votes for empty
[2026-03-09T23:39:54.736000+00:00] potuz: Lodestar has 3 missed slots in a row there that I do not know what happen, can look in logs to see if we ever saw them
[2026-03-09T23:40:22.585000+00:00] nflaig: lodestar-geth-1 is syncing from genesis
[2026-03-09T23:42:43.307000+00:00] pk910: node2 checkpoint synced 🙂
`Mar-09 23:41:42.051[]                 info: Initializing beacon from a valid checkpoint state slot=38656, epoch=1208, stateRoot=0x3f841e5d50bf611bb37a14d053726f57c41730cc6608b3711ffd3e5c7ce1f4f2, isWithinWeakSubjectivityPeriod=true`
[2026-03-09T23:46:07.520000+00:00] potuz: again forking off, will log in to look at logs
[2026-03-09T23:46:45.061000+00:00] potuz: Prysm is reorging Lodestar <@363800010518822915> did we restart nodes or something? are we running a different commit than before?
[2026-03-09T23:47:01.471000+00:00] pk910: looks like the prysm chain got canonical again..  so more lighthouse & lodestar forking off and reorging later
[2026-03-09T23:49:54.784000+00:00] nflaig: great, checkpoint sync was smooth, lodestar-geth-1 is also still syncing fine although bit slow, can restart the third node via checkpoint sync too
[2026-03-09T23:50:08.104000+00:00] pk910: these two forks happened at the moment I've restarted the lodestar nodes. 
peering doesn't look bad,  so even when loosing the lodestar node, other nodes had enough other peers.
[2026-03-09T23:51:12.512000+00:00] pk910: can try with the third node,  that still has to be rolled 😄
so,  when the chain forks again in a moment, it's likely related
[2026-03-09T23:54:12.481000+00:00] pk910: nope,  no fork this time,  third lodestar node rolled with checkpoint sync
[2026-03-09T23:54:14.783000+00:00] terencechain: No we haven't changed anything since last week
```

</details>
