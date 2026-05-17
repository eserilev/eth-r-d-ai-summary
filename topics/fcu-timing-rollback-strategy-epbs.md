# FCU Timing and Rollback Strategy for ePBS

---

## 2026-05-01 (epbs)

The discussion centers on when to send forkchoiceUpdated (FCU) calls in the context of ePBS to avoid unnecessary rollbacks. Potuz proposes that FCU should only be sent when proposing blocks (with payload attributes) rather than on every slot advance by attesters. This approach would limit FCU calls to payload insertions and reorgs, making the FCU serve as a hint to trigger block production rather than causing rollbacks.

The concern driving this proposal is that rollbacks could break applications. M.kalinin notes that most execution layer clients can only propose blocks on top of the chain head anyway, so they would need to perform rollbacks regardless. However, the goal is to avoid rollbacks for user experience reasons rather than technical limitations. The discussion reveals a tension between the need for rollbacks during block proposal (which is necessary for ePBS and honest reorgs) and the potential impact on applications.

An open question remains about the practical implications, as m.kalinin observes that DApps shouldn't be consuming data from nodes serving validator duties, suggesting there may be ways to isolate the rollback effects from end-user applications while still supporting the validator workflow requirements.

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

