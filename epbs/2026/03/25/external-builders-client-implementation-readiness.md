# External builders and client implementation readiness

**Channel:** epbs | **Date:** 2026-03-25

## Summary

The discussion focused on client readiness for external builder support in Ethereum. pawandhananjay expressed confidence that they could have implementation ready by an unspecified deadline.

terencechain clarified that external builder handling doesn't require consensus-breaking changes, providing a graceful implementation path. Clients that aren't ready can simply avoid subscribing to the proposer preference and builder bid subnets to prevent being downscored by the network. Prysm has already implemented this support, though it remains untested. At minimum, Prysm nodes can communicate among themselves on these subnets, with other clients able to join the network after implementing support and restarting.

## Participants

- pawandhananjay
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-25T01:04:06.754000+00:00] pawandhananjay: I think we can have something by then
[2026-03-25T01:50:00.371000+00:00] terencechain: One point is that handling external builders does not have to be consensus breaking. If a client is not ready, it can simply avoid subscribing to the proposer preference and builder bid subnets, so it does not get downscored. Prysm already has support for this, it has not been tested yet. At a minimum, Prysm nodes can gossip with other Prysm nodes on those subnets, and any client that is ready can join them after a restart
```

</details>
