# Index-1 Attestation Packing Issues

**Channel:** epbs | **Date:** 2026-03-09

## Summary

The discussion centers around a critical issue where no Ethereum client implementations (Prysm, Lodestar, Lighthouse) were packing index-1 attestations during skip slot events. Initially suspected to be either a specification gap or missing implementation behavior, the investigation revealed multiple contributing factors. The team discovered that blocks were showing all attestations with index 0, and there were concerns about whether index-1 attestations were even being gossiped properly since the Electra upgrade, potentially due to missing committee index topics.

The root cause was partially attributed to outdated deployments and tooling issues. The Dora block explorer wasn't properly displaying the `AttestationData.Index` field for post-Electra blocks, which was masking the actual payload status and making it difficult to diagnose the problem. Additionally, the Lodestar deployment on devnet-0 was using an outdated image that hadn't been updated with the necessary fixes for proper index-1 attestation handling.

Once pk910 updated Dora to properly render committee indices and nflaig confirmed that the latest Lodestar image should handle index-1 attestations correctly, the situation improved significantly. Terencechain reported receiving index-1 attestations from Lighthouse (230) and Prysm (50) over a few hours, though Lodestar attestations weren't visible until the deployment issue was addressed. The team initiated a build update for the devnet, though deployment automation (watchtower) had been previously disabled due to restart issues.

## Participants

- nflaig
- pk910
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T18:03:16.148000+00:00] terencechain: There's something weird going on. No one (prysm, loderstar, lightouse) proposers are packing index-1 attestations in the event of skip slot.
Not sure if there's a spec gap or no one implemented such behavior
[2026-03-09T18:21:49.017000+00:00] terencechain: <@&1460290804898861117> if your implementation actually sends index-1 for full, can you please confirm here? ill track them to make sure they get included in the block 🙏
[2026-03-09T18:59:18.953000+00:00] potuz: are we absolutely certain the checkpoint server is serving post-CL states?
[2026-03-09T18:59:23.767000+00:00] potuz: is it still prysm only?
[2026-03-09T19:05:09.952000+00:00] nflaig: our latest image should serve same states as prysm but someone has to roll out the update
[2026-03-09T19:06:05.805000+00:00] nflaig: we should be sending proper attestations with index=1
[2026-03-09T19:06:56.235000+00:00] potuz: no one is packing them
[2026-03-09T19:07:10.344000+00:00] potuz: I can' t explain why Prysm is not building on empty on some blocks
[2026-03-09T19:07:42.931000+00:00] potuz: which is our bug, but I suspect that there' s some form of packing bug, that we may have index=1 attestations in forkchoice, but just not including them onchain
[2026-03-09T19:08:00.217000+00:00] potuz: it's the only explanation I can find as to why we do not attempt to reorg payloads
[2026-03-09T19:09:01.151000+00:00] nflaig: I suspect it's that, eg. https://dora.epbs-devnet-0.ethpandaops.io/slot/37255 looks wrong if dora displays that as committee index, we haven't changed anything in our attestation packing for gloas yet, I assumed it should work without changes but maybe not, will take a look
[2026-03-09T19:10:03.509000+00:00] potuz: ahh let' s rule out actually this, will grab a block from the pods
[2026-03-09T19:11:31.961000+00:00] potuz: hmm, is the beacon running on some weird port?
[2026-03-09T19:13:54.070000+00:00] potuz: ahyeah
[2026-03-09T19:15:25.140000+00:00] potuz: that block has all index attestations being 0
[2026-03-09T19:16:40.663000+00:00] potuz: So it' s not a Dora thing
[2026-03-09T19:30:27.697000+00:00] terencechain: No one is packing index-1 attestations. Something is really off 
We should have aggregators that do this right
[2026-03-09T20:00:53.359000+00:00] potuz: do we not have topics for committee index perhaps?
[2026-03-09T20:01:03.464000+00:00] potuz: perhaps we aren' t even gossiping these
[2026-03-09T20:01:12.575000+00:00] potuz: since Electra
[2026-03-09T21:13:26.349000+00:00] pk910: I've extended the attestation list in dora. It didn't use the `AttestationData.Index` field for post-electra blocks, so the payload status was not visible.
[2026-03-09T21:14:32.884000+00:00] pk910: It's rendered as a separate badge for gloas blocks now 🙂
[2026-03-09T21:37:36.403000+00:00] nflaig: yeah that block is strange but I also found one that as index=1 attestations https://dora.epbs-devnet-0.ethpandaops.io/slot/38063, although it does seem like only prysm voted correctly
[2026-03-09T21:38:34.045000+00:00] nflaig: that is nice, no need to curl blocks locally anymore, maybe potentially confusing if the votes are for current slot
[2026-03-09T22:00:06.030000+00:00] terencechain: oh that explains it, i was going down the rabbit hole on why proposers didnt pack any index 1 attetations
[2026-03-09T22:00:56.730000+00:00] terencechain: proposer packing looks much better now
[2026-03-09T22:09:33.963000+00:00] terencechain: <@586161934425128960> my local node is not seeing any index 1 attestations from lodestar, maybe im not peered with any lodestar nodes? I'm subscribe to all the subnets. In the last few hours, i've received 230 index-1 atts from Lighthouse and 50 index-1 atts from prysm
[2026-03-09T22:22:08.241000+00:00] nflaig: can you try with latest image, still testing locally but should be fixed, our devnet-0 image deployed on the devnet is pretty outdated now
[2026-03-09T22:22:30.515000+00:00] terencechain: oh ok, i was watching devnet-0. That explains it 🙂
[2026-03-09T22:22:59.395000+00:00] nflaig: anyone can roll out a new version?
[2026-03-09T22:23:18.884000+00:00] nflaig: Barnabas usually did this
[2026-03-09T22:25:08.966000+00:00] terencechain: there's: https://discord.com/channels/595666850260713488/892088344438255616/1359822239087722598
but i haven't had much success with it
[2026-03-09T22:26:14.122000+00:00] nflaig: I think that just builds the image, not deploy it, usually we have watchtower but since we couldn't restart previously it was disabled
[2026-03-09T22:26:43.304000+00:00] terencechain: oh i see, i thought it auto deploy every hour but i could be wrong
[2026-03-09T22:58:44.595000+00:00] pk910: did you update your epbs-devnet-0 branch?
[2026-03-09T22:59:26.151000+00:00] pk910: ah yea, I see you triggered a build 45mins ago 🙂
```

</details>
