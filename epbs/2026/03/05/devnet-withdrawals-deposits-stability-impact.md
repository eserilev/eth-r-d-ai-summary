# Devnet withdrawal types and testing strategy

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion focuses on withdrawal credential types in the current devnet setup. The team clarifies that they have 0x02 withdrawal credentials only (not 0x00), which means they need to send withdrawal requests rather than rely on automatic sweeps. There's a proposal to make a 0x03 deposit, but this is deemed out of scope for epbs-devnet-0 and likely to break things.

The team identifies significant stability issues with the current devnet - Lighthouse and Lodestar clients cannot catch up after restarts, and the network is at 94% capacity. Due to these sync problems, they decide against making any changes until resync capabilities are improved. There are concerns that withdrawals could cause all clients to fail on missed slots.

The agreed testing strategy prioritizes existing structures first (normal deposits, topups, withdrawal requests) before attempting new 0x03 functionality. The consensus is to avoid touching the current setup until the underlying client sync issues are resolved.

## Participants

- barnabasbusa
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T13:09:16.233000+00:00] barnabasbusa: we don't have 0x00
[2026-03-05T13:09:17.181000+00:00] potuz: Once we have that we can send BLS changes to this devnet and if we crash we can circle back and fix
[2026-03-05T13:09:28.930000+00:00] barnabasbusa: we have 0x02 only
[2026-03-05T13:10:06.458000+00:00] potuz: ahh I assumed 0x00 because there were no withdrawals, good choice on 0x02, then we need to send withdrawal requests
[2026-03-05T13:10:08.276000+00:00] potuz: easier
[2026-03-05T13:10:30.161000+00:00] potuz: hmm, perhaps a sweep would be better in fact, not sure
[2026-03-05T13:30:09.259000+00:00] barnabasbusa: <@641548687813902336> do we have any progress on teku's side?
[2026-03-05T13:30:23.668000+00:00] barnabasbusa: <@1386598056320831509> wants to make a 0x03 deposit
[2026-03-05T13:30:29.351000+00:00] barnabasbusa: do we expect this would break stuff?
[2026-03-05T13:39:35.573000+00:00] potuz: no lets not touch this as per this please:
[2026-03-05T13:39:42.876000+00:00] potuz: this
[2026-03-05T13:40:00.314000+00:00] potuz: Pay special attention to the first sentence in this message
[2026-03-05T13:40:09.765000+00:00] potuz: please <@199561711278227457> keep <@412614104222531604> away from pods
[2026-03-05T13:41:49.284000+00:00] barnabasbusa: we are at 94%
[2026-03-05T13:42:38.705000+00:00] jtraglia: Yeah this is out of scope for epbs-devnet-0
[2026-03-05T13:43:11.689000+00:00] jtraglia: It will break things
[2026-03-05T13:43:21.339000+00:00] potuz: you don't undersand that right now if you restart LH and Lodestar they cannot catch up
[2026-03-05T13:43:32.003000+00:00] potuz: we cannot split this until the above list is implemented
[2026-03-05T13:43:35.615000+00:00] barnabasbusa: I'm not touching anything 😄
[2026-03-05T13:44:03.555000+00:00] potuz: I think it's fine to send deposits and withdrawals etc... on this devnet, but let's make sure that we can resync faster before
[2026-03-05T13:44:35.499000+00:00] potuz: Also, I would test first existing structures, then new structures. So first deposits for normal topups, withdrawal requests, etc.
[2026-03-05T13:44:41.379000+00:00] potuz: then 0x03 stuff.
[2026-03-05T13:44:57.362000+00:00] potuz: I suspect withdrawals will screw all clients on a missed slot
```

</details>
