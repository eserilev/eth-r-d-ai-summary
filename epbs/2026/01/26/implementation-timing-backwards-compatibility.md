# Implementation timing and backwards compatibility concerns

**Channel:** epbs | **Date:** 2026-01-26

## Summary

The discussion centers on Nimbus's implementation strategy for alpha.2 features that lack backwards compatibility. Tersec indicates that Nimbus will likely delay implementing these non-backwards compatible changes until the devnet phase begins, rather than maintaining separate development branches.

The rationale is primarily resource efficiency - maintaining multiple branches creates ongoing overhead through merge conflicts and rebasing that outweighs the benefits compared to implementing features once when needed. Tersec notes this approach sometimes results in delayed implementation for longer-running devnets, but considers that inefficiency preferable to the continuous maintenance burden of parallel development branches.

## Participants

- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-26T13:10:16.978000+00:00] tersec: well, just practically, Nimbus might simply not implement alpha.2 stuff which isn't backwards compatible until the devnet happens then
[2026-01-26T13:11:22.126000+00:00] tersec: maintaining multiple branches of this sort is basically wasted time. the time spent dealing with merge conflicts/rebasing/etc (ongoing, hard to schedule) is too much compared to the time needed to implement it once (not trivial, but one-time)
[2026-01-26T13:11:48.401000+00:00] tersec: and yes, we end up doing it later on sometimes for devnets which live longer than they should but I find that pointless too
```

</details>
