# Checkpoint sync issues on ePBS devnet 1

**Channel:** epbs | **Date:** 2026-04-05

## Summary

The discussion centers on checkpoint synchronization failures on the ePBS devnet 1. 0xunclebill initially suspected epoch mismatch issues but determined these were caused by stale nodes. The main problem identified was custody column requirements - their node could only connect to one peer and was struggling to find peers that could serve its custody requirements. When they attempted to set custody requirements to 0, other nodes banned them, forcing them to connect directly to a supernode.

Further investigation revealed their node was spamming Prysm nodes with blocks-by-range requests and getting banned as a result. 0xunclebill identified that checkpointz needs to implement the `GET eth/v1/beacon/payload_envelopes/{block_id}` endpoint, as their current checkpoint sync implementation attempts to fetch the envelope for the checkpoint slot before beginning range sync. While they have an alternative implementation that doesn't require checkpointz to serve envelopes, they believe the server should support this endpoint.

The session ended with checkpointz server returning 500 errors, adding to the synchronization difficulties. nflaig expressed solidarity, having experienced similar issues recently, but appears to have moved on to code review rather than continuing to debug the devnet issues.

## Participants

- 0xunclebill
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-05T06:35:08.889000+00:00] 0xunclebill: ok I think i see the reason why we cant checkpoint sync on epbs devnet 1 (at least one of the reasons)
[2026-04-05T06:37:16.175000+00:00] 0xunclebill: ~finalized epoch being served by checkpoint server is epoch 1069 but finalized epoch is 1072~
[2026-04-05T06:38:16.673000+00:00] 0xunclebill: peers refuse the handshake
`Apr 04 23:37:53.880 DEBUG Handshake Failure                             peer_id: 16Uiu2HAmEf7cqrMFSvwJn9U3zHuoiu4nd1E88i2MiBLDbVXPxGGT, reason: "Old finality out of range"`

EDIT: ah wait these are just super stale nodes we are trying to peer with, definitely not the issue
[2026-04-05T06:39:34.793000+00:00] nflaig: <@358120958726373381> let me know if you managed to do anything on devnet-1 I gave up here https://discord.com/channels/595666850260713488/874767108809031740/1489590748008415292, back to reviewing code 😄
[2026-04-05T06:44:48.490000+00:00] 0xunclebill: ok our node is waiting to find peers that can serve its custody column requirements
[2026-04-05T06:45:27.222000+00:00] 0xunclebill: its only been able to connect to one peer. are we running any super nodes?
[2026-04-05T06:45:41.333000+00:00] 0xunclebill: im gunna try setting custody req to 0 and see what happens

haah okay everyone bans me when i do that, will connect to the supernode directly instead
[2026-04-05T06:49:04.413000+00:00] nflaig: should be https://dora.epbs-devnet-1.ethpandaops.io/clients/consensus#name=prysm-geth-3
[2026-04-05T08:11:08.250000+00:00] 0xunclebill: in our case we are just spamming prysm with blocks by range requests and getting banned. something funky on our end
[2026-04-05T08:23:31.077000+00:00] 0xunclebill: I think we need checkpointz to implement `GET eth/v1/beacon/payload_envelopes/{block_id}`
[2026-04-05T08:23:59.309000+00:00] 0xunclebill: at least our current checkpoint sync impl tries to fetch the envelope for the checkpoint slot before begining range sync
[2026-04-05T08:25:20.052000+00:00] 0xunclebill: we have another checkpoint sync impl that doesnt require checkpointz server to serve envelopes. but i feel like checkpointz server should implement that endpoint
[2026-04-05T09:10:55.539000+00:00] 0xunclebill: now checkpointz server is returning 500's
[2026-04-05T09:11:25.218000+00:00] nflaig: I feel your pain, was in the same spot 2 days ago 😄
```

</details>
