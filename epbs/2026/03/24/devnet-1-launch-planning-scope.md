# Devnet 1 launch planning and timeline

**Channel:** epbs | **Date:** 2026-03-24

## Summary

The team is facing challenges with devnet 0 and is proposing to move forward with launching devnet 1 instead. The discussion centers around incorporating PTC (likely Proof-of-Stake Transition Committee) window changes, which terencechain has already implemented and is awaiting spec tests and release. There's agreement that devnet 1 will require PTC selection changes and handling of external builders, though builders can be held back initially while focusing on sync and checkpoint sync functionality.

Timeline-wise, Monday was initially suggested as a launch target, but pawandhananjay mentioned needing about a week to complete a basic version of range sync. This pushes the timeline to potentially Wednesday, which would allow the team to report progress at the All Core Developers (ACD) meeting. Before launch, the team wants to run Kurtosis checks with the PTC changes and create static vectors for the new state.

The main open question is confirming client readiness for devnet 1 synchronization, with sync and checkpoint sync being identified as mandatory requirements since the network is expected to break quickly and require reliable recovery mechanisms.

## Participants

- barnabasbusa
- pawandhananjay
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-24T21:39:26.109000+00:00] barnabasbusa: I think we are struggling to make devnet 0 work again. So I’d propose that we aim to do a launch of devnet 1, and try not to break that 🫣
[2026-03-24T22:54:39.254000+00:00] terencechain: timeline? should we get the PTC window change in? i already implemented it, just waiting for the spec tests + release
[2026-03-24T23:40:33.853000+00:00] potuz: I for once agree with <@412614104222531604>, can we get a thumbs up from clients that they'll be ready to sync on devnet 1, this one will require the PTC selection changes and also handling of external builders but that one we can agree on not sending any until we are ready. However: sync and checkpoint sync should be mandatory cause we're sure to break quickly
[2026-03-24T23:41:30.077000+00:00] potuz: Monday is as good a day as any for me and it happens to be after a weekend which is a plus
[2026-03-24T23:42:19.796000+00:00] potuz: Although probably we want to run some Kurtosis checks with the PTC changes before and static vectors for the new state
[2026-03-24T23:44:40.173000+00:00] pawandhananjay: what's the timeline for devnet 1? I think we should have a basic version of range sync in a week or so
[2026-03-24T23:48:19.491000+00:00] potuz: Then Monday is out of the question we can try to aim for Wednesday? So that we can report at ACD?
```

</details>
