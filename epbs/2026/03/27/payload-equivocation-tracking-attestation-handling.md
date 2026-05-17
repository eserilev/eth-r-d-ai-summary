# Payload equivocation tracking and attestation handling issues

**Channel:** epbs | **Date:** 2026-03-27

## Summary

Potuz reported an issue discovered by a team member related to the lack of payload equivocation tracking in the current system. The core problem is that attestations with `index=1` should not be rejected when a payload is invalid, since there may be an equivocation scenario where a valid payload exists. Potuz suggests that a proper fix would involve adding an `envelope_root` to the signed envelope, with attesters referencing this envelope root instead of the beacon block root as head, which would also help resolve branches during equivocations.

The discussion highlights broader concerns about handling payload equivocations, with Potuz noting that there are likely many edge cases that complicate the design of a non-DoS-able pending payload queue. The issue appears to be related to a PR written by another team member, and Terencechain confirmed that Subhasish has identified additional related problems, though the specific details of these new issues weren't elaborated in the provided messages. The suggestions are noted as considerations for the next iteration of forkchoice rather than immediate fixes.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-27T11:18:18.286000+00:00] potuz: <@908359967038382121> has found another issue that stems from the fact that we do not track payload equivocations. Late to fix this properly but I suspect that we should have added an  envelope_root to the signed envelope and just keep things referencing that HTR. In particular, instead of setting index=1, attesters would attest to that envelope root instead of the beacon block root as head. It would also resolve branches on equivocations. Something to keep in mind for the next iteration of forkchoice.
[2026-03-27T11:26:48.775000+00:00] potuz: FWIW, the issue is that we should not `REJECT` attestations with `index=1` when the payload is invalid. There may be an equivocation with a valid payload. I'm pretty certain that there are a lot of edge cases when dealing with payload equivocations. Designing the pending payload queue in a non-dossable manner sucks because of this. cc <@363800010518822915> who wrote the PR.
[2026-03-27T14:05:00.199000+00:00] terencechain: @Subhasish has found another issue
```

</details>
