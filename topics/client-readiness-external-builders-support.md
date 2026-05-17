# Client readiness and external builders support

---

## 2026-03-25 (epbs)

The discussion focuses on client implementation timelines and strategies for supporting external builders without breaking consensus. Pawandhananjay indicates they believe their client can have something ready by an unspecified deadline.

Terencechain clarifies that external builder support can be implemented in a non-consensus-breaking manner, where clients that aren't ready can simply avoid subscribing to the proposer preference and builder bid subnets to prevent downscoring. Prysm has already implemented this functionality, though it remains untested. The current setup allows Prysm nodes to gossip among themselves on these subnets, with other clients able to join after implementing support and restarting.

This approach provides a graceful migration path where clients can opt into external builder functionality when ready, rather than requiring all clients to implement support simultaneously.

**Participants:** pawandhananjay, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-25T01:04:06.754000+00:00] pawandhananjay: I think we can have something by then
[2026-03-25T01:50:00.371000+00:00] terencechain: One point is that handling external builders does not have to be consensus breaking. If a client is not ready, it can simply avoid subscribing to the proposer preference and builder bid subnets, so it does not get downscored. Prysm already has support for this, it has not been tested yet. At a minimum, Prysm nodes can gossip with other Prysm nodes on those subnets, and any client that is ready can join them after a restart
```

</details>

