# Sync Implementation and Devnet Stability Issues

**Channel:** epbs | **Date:** 2026-02-28

## Summary

The discussion reveals significant concerns about sync implementation readiness for an upcoming devnet deployment. The team lacks a complete sync solution, with 0xunclebill noting that any crashes would be catastrophic since there's no recovery mechanism until the next devnet iteration.

stefanbratanov reports having only forward sync implemented, which requires tweaking to handle envelope requests by root during gossip syncing, while backfill sync remains unaddressed. Similarly, potuz indicates their team is in a comparable situation but plans to attempt a last-minute workaround solution.

The conversation concludes with humorous acknowledgment that without proper sync implementation, the devnet will likely require frequent manual restarts every few minutes, highlighting the instability expected from deploying without complete sync functionality.

## Participants

- 0xunclebill
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-28T04:07:43.032000+00:00] 0xunclebill: we don’t have sync ready. if we crash it’s gg until the next devnet 😬
[2026-02-28T09:46:59.429000+00:00] stefanbratanov: Only got forward sync implemented, need some tweaking to request envelopes by root in certain cases when gossip syncing, backfill I haven't touched
[2026-02-28T10:28:46.201000+00:00] potuz: Same for us, but we'll try to hack something at the last hour. Otherwise it'll be <@412614104222531604> restarting devnet-0 every 5 minutes 🤣
```

</details>
