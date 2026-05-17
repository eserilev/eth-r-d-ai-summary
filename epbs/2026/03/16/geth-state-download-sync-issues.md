# Geth State Download and Sync Issues

**Channel:** epbs | **Date:** 2026-03-16

## Summary

Potuz reported experiencing unusually slow state download times with Geth during a checkpoint sync operation, initially suspecting potential network spam as the cause. They expected the sync to complete within 10 minutes but were still waiting for it to finish.

Upon further consideration, potuz realized that Geth likely needs to download all blocks following the checkpoint sync, which explains the extended sync time. They noted this could become a significant issue over longer periods, particularly after a couple of weeks when the number of blocks to download would substantially increase.

The discussion identifies a potential scalability concern with Geth's post-checkpoint sync process, though no specific solutions or action items were proposed.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T12:45:54.848000+00:00] potuz: Geth is taking a long time to download an empty state, has it been spammed?
[2026-03-16T12:46:16.221000+00:00] potuz: I just checkpoint synced and I was expecting to be online after 10 minutes, but I'm still optimistic
[2026-03-16T12:47:00.688000+00:00] potuz: Ah It probably needs to download now all blocks actually
[2026-03-16T12:47:10.109000+00:00] potuz: after a couple of weeks that may be a problem here
```

</details>
