# CL client sync EIP proposal without payloads

**Channel:** epbs | **Date:** 2026-04-22

## Summary

Potuz proposed a new EIP that would allow Consensus Layer (CL) clients to sync without requesting payloads, suggesting that finalized envelopes could be pruned and unfinalized sections could sync without payload requests. The proposal has been submitted as GitHub pull request #11557 and is awaiting authorization to add additional authors.

The discussion briefly addressed data availability concerns, with nflaig asking about data columns that nodes need to custody. Potuz clarified that data availability (DA) functionality remains unchanged since KZG commitments are included in the bid, meaning the proposal doesn't impact existing DA requirements.

The EIP is currently under review, with Potuz requesting feedback from other developers on the technical approach and implementation details.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-22T20:20:48.949000+00:00] potuz: Added this EIP to allow CL clients to sync without even requesting payloads, I think we can (and therefore should) prune all finalized envelopes and even on the unfinalized section we can sync without requesting payloads. https://github.com/ethereum/EIPs/pull/11557
[2026-04-22T20:21:05.104000+00:00] potuz: Could you guys please take a look? also awaiting for authorization to add authors 🙂
[2026-04-22T20:24:17.171000+00:00] nflaig: what about data columns that the node needs to custody? I guess that is fine since the kzg commitments are part of the bid
[2026-04-22T20:25:03.724000+00:00] potuz: yeah DA doesn't change at all
```

</details>
