# Finalization Achievement and Node Updates

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around achieving finalization on what appears to be a test network and subsequent node updates. Potuz reported successful finalization and proceeded to start beacon nodes on prysm instances 2 and 3, noting that their validators might require restarts once the beacon nodes catch up to the current head.

After monitoring the network, Potuz confirmed that the chain had stabilized with all prysm nodes synchronized to head and actively proposing blocks. However, he noted a remaining issue where the network experiences reorgs on any missed slots, which could lead to occasional missed slots but should prevent chain forks from occurring. The team appears satisfied with the current stable state of the network.

## Participants

- potuz
- samcm

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T03:01:59.007000+00:00] potuz: ok, we've finalized, <@84562395988508672> I'll try to do those on prysm 2 and three
[2026-03-05T03:15:57.100000+00:00] potuz: <@84562395988508672> I started the beacons on prysm 2 and 3 they may need to have their validators restarted later
[2026-03-05T03:16:21.433000+00:00] potuz: I may stay a little longer to check if they propose but if they aren't just restart them when the beacons get to head
[2026-03-05T03:19:33.101000+00:00] samcm: Yup too easy
[2026-03-05T03:19:34.734000+00:00] samcm: Ty!
[2026-03-05T03:46:52.413000+00:00] potuz: chain should be pretty stable now, all prysm nodes are up to head proposing. We still get reorged on any missed slot, so we may miss some slots, but we shouldn't fork
[2026-03-05T03:47:20.686000+00:00] potuz: 
```

</details>
