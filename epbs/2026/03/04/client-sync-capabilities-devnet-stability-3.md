# Network Fork and Client Synchronization Issues

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers on a network fork that occurred on an Ethereum devnet where different client implementations (Prysm, Lighthouse, Lodestar, and Teku) ended up on separate branches. Prysm isolated itself on its own branch despite syncing blocks from Lighthouse and Lodestar, refusing to accept them as head because it believes those clients should have reorged a payload based on attestation data. The team identified that Prysm was building blocks on top of empty payloads due to attestations directing it to do so, and there were issues with slot 355 having two proposers and Teku being on an extended fork.

Potuz discovered a bug in one of the Prysm nodes around slot 362 and noted that the network was experiencing peer downscoring issues that prevented proper payload envelope retrieval. While Prysm's team believes their client's behavior is technically correct, they acknowledge having less voting power than the Lighthouse+Lodestar coalition. The main unresolved question is why peers aren't providing the parent payloads that Prysm requests via RPC, and there's uncertainty about whether Prysm will perform a large reorg to join the majority branch even if their interpretation proves correct.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T15:53:01.426000+00:00] potuz: where is Dora
[2026-03-04T15:53:08.467000+00:00] potuz: we split a few slots ago
[2026-03-04T15:54:04.147000+00:00] potuz: Prysm is on it's own branch, we still sync the blocks from Lodestar and Lighthouse but we don't take them as head
[2026-03-04T15:55:44.031000+00:00] barnabasbusa: prysm does not look too hot
[2026-03-04T15:56:01.631000+00:00] potuz: I think Prysm is right though
[2026-03-04T15:56:15.907000+00:00] potuz: Prysm is building blocks on top of empty and getting reorged
[2026-03-04T15:56:27.979000+00:00] potuz: I need to check if the attestations are with index=0 or index=1
[2026-03-04T15:56:56.703000+00:00] potuz: From what I see on logs Prysm is building on empty because attestations told them to
[2026-03-04T15:58:01.198000+00:00] potuz: we will soon die with this split though
[2026-03-04T16:02:30.822000+00:00] potuz: https://dora.epbs-devnet-0.ethpandaops.io/slot/353
[2026-03-04T16:02:41.058000+00:00] potuz: I can only find attestations for the missing payload
[2026-03-04T16:10:40.089000+00:00] potuz: yeah thanks, found a bug on one of the Prysm nodes (or at least one of the two was not acting correctly at the forked slot in 362)
[2026-03-04T16:12:30.197000+00:00] potuz: ahhh shit I was confused, Teku is on a seriously long fork, 355 has two proposers
[2026-03-04T16:13:41.142000+00:00] potuz: We are downscoring peers and then fail to get the payload envelope
[2026-03-04T16:13:58.010000+00:00] barnabasbusa: prysm seems to be on 2 forks away from the head
[2026-03-04T16:14:08.049000+00:00] potuz: they are in their own branch
[2026-03-04T16:14:24.425000+00:00] potuz: Prysm will not go with LH and Lodestar cause it thinks they should have reorged a payload
[2026-03-04T16:14:34.817000+00:00] potuz: and I think Prysm is actually right, but has less voting power
[2026-03-04T16:14:49.838000+00:00] potuz: I do not now why it doesn't do a huge reorg though
[2026-03-04T16:18:12.137000+00:00] potuz: But the fact that it is only paired to Prysm at that point It means we're isolated, I don't think we'll go to LH+Lodestar's branch even if ours  turns out to be correct, we can try with the syncing branch if you want
[2026-03-04T16:18:34.962000+00:00] potuz: But I want to understand why peers aren't even providing our parent payloads that we request by RPC
```

</details>
