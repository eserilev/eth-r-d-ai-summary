# Prysm Syncing and Peer Discovery

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around troubleshooting Prysm consensus layer (CL) client syncing on a new devnet. Parithosh mentions refreshing CL ENRs (Ethereum Node Records) for peer discovery, which is typically required for first runs on new devnets, though in this case a peer was found without needing the refresh. 

Potuz reports successfully finding a peer and that the client has begun syncing by range. Notably, they observe that the peer connection is inbound rather than outbound. The conversation is briefly interrupted as parithosh steps away for a quick errand, leaving the syncing process in progress.

## Participants

- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T17:10:27.756000+00:00] parithosh: refreshing my CL enrs and checking
[2026-03-04T17:10:28.871000+00:00] potuz: ah found one
[2026-03-04T17:10:36.935000+00:00] parithosh: i hadnt done that and usually need to for first run on a new devnet
[2026-03-04T17:10:41.524000+00:00] parithosh: but yeah, it found one anyway
[2026-03-04T17:10:53.579000+00:00] potuz: it's syncing by range now
[2026-03-04T17:11:11.199000+00:00] parithosh: i gotta quickly get something from the supermarket before they close, will be back in ~15min
[2026-03-04T17:11:14.348000+00:00] potuz: interesting the peer is inbound
```

</details>
