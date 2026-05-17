# Epoch Transition Architecture Changes

---

## 2026-04-14 (epbs)

The discussion centers on architectural challenges introduced by a PR that requires running epoch transitions before calling FCU (Fork Choice Update) when proposing blocks at epoch boundaries. Potuz identifies this as a significant architectural change that will necessitate complex NSC (Node State Changes) modifications they had hoped to avoid.

The participants reveal different client implementations handle this differently - Lodestar already runs state/epoch transitions before FCU calls in their pre-gloas implementation, while Potuz's client sends many operations to background processes to avoid blocking the critical attestation path. To accommodate the change, they're considering sending two separate FCUs to the execution layer - one with and one without payload attributes.

Nflaig notes a potential benefit of proposal 5094: epoch transitions can be run earlier in the previous slot since there's no need to wait for payload arrival (which could be delayed by PTC deadlines). This could provide more time for the transition process, though the architectural complexity remains a concern that needs resolution.

**Participants:** nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-14T18:27:15.466000+00:00] potuz: I think the biggest problem of your PR <@586161934425128960> as of now is that you need to do an epoch transition **before** you call FCU to propose next block at epoch boundary. It's not much of a problem, but this is an architerctural change for us
[2026-04-14T18:27:52.747000+00:00] potuz: there's no way to avoid this thus, we'll have to bite the bullet and do the complicated NSC changes that I was afraid of, cc <@363800010518822915>
[2026-04-14T18:29:40.921000+00:00] nflaig: that's true, however you can do that as soon as you have the beacon block, so timing wise seems fine, architecture wise it might be different based on client, we already run state/epoch transition before calling FCU in lodestar in pre-gloas
[2026-04-14T18:31:42.334000+00:00] potuz: we don't we send many of these things to the background to not lock on the hot path of attestation. We'll need to see what to do, probably we'll end up sending two different FCUs to the EL. one with and another without payload attributes
[2026-04-14T18:31:46.395000+00:00] nflaig: on the flip side, the benefit here of 5094 compared to before is that you can run the epoch transition much earlier into the previous slot since you not longer have to wait for the payload to arrive (which could be late depending on ptc deadline), let's say 9 seconds, then you only have 3 seconds to do the epoch transition, and likely clients would have to run 2 epoch transition just to be prepared
```

</details>

