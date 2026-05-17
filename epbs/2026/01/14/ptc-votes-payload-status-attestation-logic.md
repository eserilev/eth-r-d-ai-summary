# PTC Votes in Blocks and Network Synchronization

**Channel:** epbs | **Date:** 2026-01-14

## Summary

This discussion explores why PTC (Payload Timeliness Committee) votes need to be included in blocks rather than remaining off-chain. The core issue is network synchronization during potential attacks where the network gets split and attesters don't see PTC votes. By including PTC votes in blocks, honest proposers can synchronize attesters' views and force consensus regardless of what attesters saw locally. The proposer has the ability to build on "full" payloads even if the majority of PTC votes indicate "empty," but the main use case is for proposers to force empty blocks when needed.

The technical rationale centers around ensuring network consistency. Attesters who don't have the payload won't attest to the proposer's block, so if there's a PTC quorum indicating the payload is missing, it's rational to build on the missing payload. If there's no quorum, building on the present payload by default makes sense. PTC votes also play a role in tie-breaking scenarios. Without including PTC votes on-chain, there's no guarantee attesters will receive them, creating risks of view divergence between proposers and attesters.

An open question remains about whether PTC votes could stay off-chain to reduce on-chain overhead, with the discussion noting that while the votes are small, minimizing unnecessary on-chain data would still be preferable if technically feasible.

## Participants

- jih2nn
- julianma_
- nero_eth
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-14T00:51:04.183000+00:00] terencechain: So the attack is someone splitting the network so attesters do not see PTC votes, and including PTC votes in the block allows honest proposers to synchronize honest attestor's view
[2026-01-14T02:10:33.067000+00:00] potuz: There's no guarantee that attesters got the PTC vote. If the proposer got a consensus to reorg or to build on top of the Payload, by including the votes in the block they make sure that attesters have to follow their view regardless of what the attesters local view was
[2026-01-14T06:27:57.306000+00:00] nero_eth: The proposer can always build on top the full payload of the prev slot, even if the PTC voted for "not available", right?
So, having the PTC votes on chain is only needed for proposers that want to enforce the PTC's "not-available" vote?
[2026-01-14T07:43:07.390000+00:00] julianma_: What happens if the proposer doesn't include PTC votes?
[2026-01-14T07:58:20.115000+00:00] terencechain: Yes. Proposer has the ability to extend "full" even majority PTC votes "empty". Attesters will go with full 

So if I'm not wrong, the main use case is for proposer to force empty
[2026-01-14T08:25:38.161000+00:00] jih2nn: PTC votes weigh in tie breaking, too
[2026-01-14T09:03:45.530000+00:00] julianma_: Oke that makes sense. Is there a reason it was chosen that empty blocks are forced by the proposer including payload attestations instead of attesters listening to PTC votes and the votes remaining off-chain?
[2026-01-14T09:04:34.348000+00:00] julianma_: Why is it not guaranteed that attesters get the PTC votes? Is it then guaranteed that the proposer gets the PTC votes?
[2026-01-14T09:42:21.127000+00:00] potuz: Attesters that don't have the Payload won't attest to the proposers block. So if there's a PTC quorum on missing, it's rational to build on missing. If there's no quorum then it makes sense to build on present by default
[2026-01-14T09:42:50.440000+00:00] potuz: Nothing. It risks his view being different than that of attesters
[2026-01-14T11:28:25.964000+00:00] julianma_: I'm asking about the PTC votes because I was thinking they won't need to go on-chain necessarily. I think the crux is whether attesters see PTC votes which makes sense to me since the proposer needs to see them as well. I understand the PTC votes are small but still it would be good not to have items on-chain if not necessary 🙂
```

</details>
