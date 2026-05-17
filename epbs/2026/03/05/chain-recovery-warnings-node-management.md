# Chain Recovery Warnings and Node Management

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz issued urgent warnings about a critical chain recovery situation affecting Ethereum client nodes. They specifically cautioned against restarting Lighthouse (LH) or Lodestar nodes because these clients cannot properly restart in the current state, which would effectively kill the chain since the network depends on nodes that have the necessary blocks and payloads.

The situation is complicated by the absence of checkpoint sync functionality, making it extremely difficult to resync any pods that become stuck. Potuz revealed they had already restarted all Prysm pods and explicitly directed another team member not to restart their nodes. At the time of the discussion, only Prysm and Lighthouse were capable of serving payloads, making the remaining operational nodes critical for chain continuity.

The discussion highlights an active crisis management scenario where maintaining specific nodes online is essential to prevent chain failure, with recovery options severely limited due to missing checkpoint sync capabilities.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T10:57:24.542000+00:00] potuz: Do not restart LH or lodestar cause they can't restart, if you do that the chain will be dead, we need someone with the blocks and payloads.
[2026-03-05T10:59:07.662000+00:00] potuz: There's also no checkpoint sync so it'll be painful to resync those pods if they are actually stuck
[2026-03-05T11:11:33.134000+00:00] potuz: please do not restart your nodes now, at least you serve blocks
[2026-03-05T11:11:42.037000+00:00] potuz: I restarted all prysm pods
[2026-03-05T11:11:54.131000+00:00] potuz: please <@412614104222531604> do not restart them
[2026-03-05T11:12:06.855000+00:00] potuz: only Prysm and LH currently serve Payloads
```

</details>
