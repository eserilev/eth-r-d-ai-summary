# Optimistic sync and execution requests in payload envelope

---

## 2026-04-10 (epbs)

The discussion centers on the necessity of including `execution_requests` in the `ExecutionPayloadEnvelope` structure. Initially, nflaig suggested that `execution_requests` might not be needed in the envelope if `engine_newPayload` could return them instead. However, upon further consideration, they realized that keeping `execution_requests` in the `ExecutionPayloadEnvelope` is actually necessary to support optimistic sync functionality.

This appears to be a brief technical clarification rather than an extended debate, with the conclusion being that the current design requiring `execution_requests` in the payload envelope should be maintained due to optimistic sync requirements.

**Participants:** nflaig

<details>
<summary>Raw messages</summary>

```
[2026-04-10T12:17:22.699000+00:00] nflaig: >  I think we don't even need execution_requests in the ExecutionPayloadEnvelope anymore but that would require engine_newPayload to return them instead
ah realized this is necessary for optimistic sync
```

</details>

---

## 2026-04-20 (epbs)

This discussion explores strategies for handling late payload requests and optimistic sync in Ethereum clients. When a current slot's payload is late, the consensus appears to be waiting for a block that builds on it before requesting the payload, which enables deterministic peer attribution rather than requesting immediately.

Potuz is investigating allowing nodes to sync child blocks without having seen the parent payload, while avoiding triggering optimistic mode or changing the forkchoice view. There's agreement that this approach may be safe for range syncing but inherently unsafe for gossip-based sync.

A key open question centers on how optimistic mode should be represented in fork choice post-Gloas. Previously, blocks were marked as "Optimistic" in fork choice with an execution_status field, but post-Gloas range sync may require an optimistic state in `PayloadStatus` to handle validation and pruning of optimistic chains. The discussion leaves unresolved whether this should be part of the specification or remain an implementation detail, with potuz noting that the current methods for invalidating optimistic branches are among the most complex in forkchoice and would likely require spec changes.

**Participants:** pawandhananjay, potuz

<details>
<summary>Raw messages</summary>

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

