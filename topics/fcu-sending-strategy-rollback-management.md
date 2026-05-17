# FCU Sending Strategy and Rollback Management

---

## 2026-05-01 (epbs)

The discussion centers on a proposed change to FCU (forkchoiceUpdated) sending strategy where FCUs would only be sent when proposing blocks (with payload attributes) rather than on every slot advance or by attesters. Potuz argues this approach would treat FCUs more as hints to trigger block production rather than causing rollbacks, since they would only be sent during payload insertions or reorgs, not simple slot advances.

A key concern raised is that rollbacks triggered by FCUs could break applications, leading to the proposal that the consensus layer should track rollbacks internally rather than forcing them on the execution layer for UX reasons. The participants note that most execution layer clients can only propose blocks on top of the chain head anyway, so they perform rollbacks regardless, but the goal is to avoid unnecessary rollbacks that aren't due to technical limitations.

The discussion touches on how this relates to ePBS (enshrined Proposer-Builder Separation) and honest reorgs, with an acknowledgment that rollbacks will still occur during legitimate reorgs and block proposals, but applications shouldn't be consuming data from nodes serving validator duties where these rollbacks would be problematic.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-01T07:46:38.324000+00:00] m.kalinin: <@755590043632140352> let's continue here:
> I think we should send that FCU only when proposing

Does that mean no FCUs by attesters?
[2026-05-01T07:58:50.342000+00:00] potuz: exactly. In the normal scenario it would mean to send FCU only on payload insertions
[2026-05-01T07:58:55.395000+00:00] potuz: or reorgs
[2026-05-01T07:59:03.558000+00:00] potuz: but not simply on slot advance
[2026-05-01T08:00:35.912000+00:00] potuz: So, **only** send that FCU that causes a rollback if it goes with Payload attributes. This way that FCU is simply a hint to trigger block production more than a rollback
[2026-05-01T08:06:26.051000+00:00] potuz: yeah this was a bug that was hit by Lighthouse when the first block was empty, the EL fails to propose in that case
[2026-05-01T08:08:25.419000+00:00] m.kalinin: oh, i see what you mean now. then CL should track rollbacks. Most of ELs are able to propose blocks on top of the head of the chain only, so they have to do a rollback in this case anyway
[2026-05-01T08:08:46.121000+00:00] m.kalinin: But we don't want to do rollbacks not due to EL incapability but due to UX reasons?
[2026-05-01T08:12:48.806000+00:00] potuz: I suspect that rollbacks can break applications
[2026-05-01T08:12:57.829000+00:00] m.kalinin: Oh, I see
[2026-05-01T08:13:06.606000+00:00] m.kalinin: Then ePBS can break applications
[2026-05-01T08:13:19.860000+00:00] potuz: not ePBS, sending that FCU
[2026-05-01T08:13:37.329000+00:00] potuz: we **only** send FCU when we import a payload, so that rollback can never happen
[2026-05-01T08:13:45.015000+00:00] m.kalinin: actually, ePBS rolls back, or any other reorg back to ancestor (honest reorgs also do)
[2026-05-01T08:14:41.748000+00:00] m.kalinin: we need that rollback to happen when we propose a block, but no dapps should be consuming data from a node serving validator duties, hmm
```

</details>

---

## 2026-05-02 (epbs)

terencechain provided a side comment suggesting that people concerned about rollback tracking could monitor the head event endpoint via the beacon API, which indicates whether blocks are empty or full. This approach would provide the same information that the consensus layer (CL) obtains when calling the forkchoiceUpdated (FCU) method and retrieving data through the execution layer's endpoint.

The comment appears to be offering an alternative method for rollback detection using existing beacon API infrastructure rather than implementing new tracking mechanisms.

**Participants:** terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-02T13:18:19.516000+00:00] terencechain: side comment, ppl that care about rollback could also track head event end point via beacon api that should indicates empty/full, this is the same as CL calling FCU and getting it via execution's end point
```

</details>

