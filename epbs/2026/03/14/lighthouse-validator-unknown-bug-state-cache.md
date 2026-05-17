# Lighthouse ValidatorUnknown Bug and State Cache Issues

**Channel:** epbs | **Date:** 2026-03-14

## Summary

This discussion involves a "ValidatorUnknown" bug occurring in Lighthouse, though the initial message appears incomplete. The issue may be related to Lodestar's state cache implementation, which is currently being reworked.

According to nflaig, the bug might originate from their side rather than Lighthouse itself. With the latest `epbs-devnet-0` image, Lodestar should only be serving post block states, suggesting this change might be connected to resolving the validator recognition issue. The discussion appears to be ongoing with the state cache rework still in progress.

## Participants

- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-14T22:34:45.613000+00:00] nflaig: Lighthouse: ValidatorUnknown - doesn't
[2026-03-14T22:36:26.600000+00:00] nflaig: might be a bug on our side, we are still reworking our state cache but with latest `epbs-devnet-0` image lodestar should only be serving post block states
```

</details>
