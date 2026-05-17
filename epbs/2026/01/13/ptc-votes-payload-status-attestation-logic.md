# PTC votes onchain rationale and implementation

**Channel:** epbs | **Date:** 2026-01-13

## Summary

The discussion centers on whether PTC (Payload Timeliness Committee) votes are stored onchain and the rationale behind this design choice. jtraglia confirms that PTC votes do go onchain, specifically at `BeaconBlockBody.payload_attestation` in the consensus specs, with potuz explaining that this allows proposers to impose their view of payload timeliness.

julianma_ questions whether onchain storage is necessary, suggesting that attesters could listen for PTC votes off-chain and punish proposers who ignore timely votes by not voting for their blocks. However, jtraglia points out that this approach would ignore the "timeliness" aspect of attestations and force other nodes to trust the proposer without verifiable proof. julianma_ counters that there's already an honesty assumption on the PTC not to release votes late, and notes that attesters must monitor excluded votes regardless to prevent proposer manipulation.

The discussion remains unresolved, with julianma_ seeking clarification on jtraglia's point about nodes having to trust proposers without onchain payload attestations. The core tension appears to be between different approaches to ensuring proposer accountability for including timely PTC votes.

## Participants

- jtraglia
- julianma_
- potuz
- soispoke

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-13T19:05:37.968000+00:00] soispoke: Hi! Quick check: do PTC votes go onchain? And if they do, what’s the main reason?
[2026-01-13T19:11:32.254000+00:00] jtraglia: They are onchain. At [`BeaconBlockBody.payload_attestation`](https://github.com/ethereum/consensus-specs/blob/315354d67e04f31e8257ef280bb4cf63076e9c3a/specs/gloas/beacon-chain.md?plain=1#L328).
[2026-01-13T19:16:25.964000+00:00] jtraglia: As for why they're onchain, I'm not actually sure. cc <@755590043632140352>.
[2026-01-13T19:17:07.620000+00:00] potuz: They allow the proposer to impose its view
[2026-01-13T20:40:09.391000+00:00] julianma_: Why do the votes need to be onchain for the proposer to impose its view?
[2026-01-13T20:41:24.504000+00:00] julianma_: The attesters of the next slot could listen for PTC votes and if the proposer should've built on the payload but didn't they could not vote for the block?
[2026-01-13T20:44:48.942000+00:00] jtraglia: Wouldn't that ignore the "timeliness" part of the attestation? And without the payload attestations in the block body, other nodes must trust the proposer.
[2026-01-13T20:49:08.294000+00:00] julianma_: Happy to be corrected, but iirc there is an honesty assumption on the PTC. It is assumed the PTC doesn't release late.

Also, the attesters need to listen for timely PTC votes that were not included anyway because otherwise the proposer could exclude votes without repercussion
[2026-01-13T20:51:00.408000+00:00] julianma_: > And without the payload attestations in the block body, other nodes must trust the proposer.

What do you mean with this exactly?
```

</details>
