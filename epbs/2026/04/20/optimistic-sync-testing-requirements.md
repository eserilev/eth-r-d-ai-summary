# Optimistic Sync and Payload Request Strategies

**Channel:** epbs | **Date:** 2026-04-20

## Summary

The discussion centers on payload request strategies and optimistic sync implementation in the context of Gloas (a new Ethereum protocol upgrade). Potuz asked about timing strategies for requesting payloads when the current slot's payload is late, and pawandhananjay confirmed they only request payloads when receiving something that builds on them, ensuring deterministic peer attribution.

Potuz is experimenting with allowing nodes to sync child blocks without having seen the parent payload, while avoiding triggering optimistic mode or changing the forkchoice view. He believes this approach could be safe for range syncing but considers it inherently unsafe for gossip-based syncing, which pawandhananjay agrees with.

The main open question involves how optimistic mode should be represented in fork choice post-Gloas. Previously, blocks were marked as "Optimistic" in fork choice with an execution_status field, allowing ancestor validation chains. Post-Gloas, range-syncing beacon blocks optimistically may require an optimistic state in PayloadStatus to handle validation and pruning of optimistic chains. Potuz notes that implementing this change would require updating the optimistic sync specification, particularly the complex methods for invalidating optimistic branches in forkchoice.

## Participants

- pawandhananjay
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-20T18:30:24.444000+00:00] potuz: Hello, another question for gloas CL devs: when the current slot's payload is late, do you request it right away or do you wait for a block built on it to arrive?
[2026-04-20T18:33:43.874000+00:00] pawandhananjay: we only request when we receive something that builds on it for deterministic peer attribution
[2026-04-20T18:34:35.921000+00:00] potuz: Thanks <@491624610215886849>, I'm trying to experiment with the notion of allowing to sync those child nodes for which we haven' t seen the parent payload, without triggering the node to be optimistic and without changing the forkchoice view of the node
[2026-04-20T18:35:11.007000+00:00] potuz: need to see if there's a safe way, but I'm convincing myself that for range syncing that seems to be safe, for gossip it seems inherently unsafe
[2026-04-20T19:19:09.820000+00:00] pawandhananjay: Yeah, agree on gossip.
For range sync, the part I’m still unsure about is how optimistic mode should be represented in fork choice post-Gloas.
Pre-Gloas, we had an execution_status in fork choice, and optimistic sync was implemented by marking blocks Optimistic. Later, an EL valid response on newPayload/fcu lets us walk ancestors back from that point and convert optimistic -> valid. An invalid response invalidates the invalid branhc, recomputes head and restarts sync from there

 Post-Gloas, if we range-sync beacon blocks optimistically, it seems like we need an optimistic state in `PayloadStatus` so that a VALID response can validate the optimistic chain and an invalid response can prune it. do you think that should be part of the spec or implementation detail?
[2026-04-20T23:12:20.169000+00:00] potuz: Eventually if we go with this we will have to change the optimistic sync spec. Currently our methods to set an optimistic branch to invalid is one of the most complicated methods in forkchoice
```

</details>
