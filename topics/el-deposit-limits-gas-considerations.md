# EL Deposit Limits and Gas Considerations

---

## 2026-05-08 (epbs)

The discussion centers on managing Ethereum deposit limits and signature verification performance, with three main approaches being debated. The first option involves implementing a queue system for deposits with signature verification while raising the SSZ limit. The second approach would lower the SSZ limit and make execution layer (EL) clients aware of this constraint. The third option suggests simply raising the SSZ limit and relying on high gas costs (potentially 200-400m gas) to naturally limit abuse, though concerns exist about recovery time when processing maximum deposits like 8192 builder deposits.

The conversation shifts toward enforcing limits directly at the EL block building level rather than implementing a separate queue system. Two specific limits are discussed: 512 deposits per block, which may still pose signature verification challenges but reduces queue hogging costs, and 16 deposits per block, which is safer for verification but highly restrictive and vulnerable to queue manipulation. The team appears to be gravitating toward a 512-deposit limit enforced on the EL side, which would eliminate the need for queuing mechanisms and allow the mempool to handle deposit prioritization while preventing issues with top-up deposits exceeding limits.

Key open questions remain around whether 512 signature verifications per block is acceptable from a performance standpoint, and how the system would recover from maximum deposit scenarios in real devnet conditions.

**Participants:** barnabasbusa, jtraglia, m.kalinin, parithosh

<details>
<summary>Raw messages</summary>

```
[2026-05-08T00:56:57.326000+00:00] jtraglia: Surely ELs can just be smart enough to not create a payload with more than the SSZ limit.
[2026-05-08T00:57:38.921000+00:00] jtraglia: I feel like there are two options: (1) queue deposits and sig verifications + raise the SSZ limit or (2) lower the SSZ limit and make ELs aware of this.
[2026-05-08T00:57:59.268000+00:00] jtraglia: But you are saying: (3) just raise the SSZ limit and hope it's not an issue because it's expensive.
[2026-05-08T01:00:05.867000+00:00] jtraglia: Isn't 200m gas limit the _floor_? I thought we were shooting for like 400m gas in the near future.
[2026-05-08T01:01:26.338000+00:00] jtraglia: <@808969530608451584> what happens in your kurtosis devnet when there are 8192 new builder deposits? How long does it take to recover?
[2026-05-08T07:41:56.910000+00:00] parithosh: wouldn't it be easier to enforce the limit on the EL block building logic instead of a second queue? if we have over x deposits in a block then consider it invalid?
[2026-05-08T07:52:32.728000+00:00] m.kalinin: it depends on the limit
[2026-05-08T07:52:44.878000+00:00] m.kalinin: if it's a 512 it might still be high. If it's 16 then it's probably fine from sig verification perspective, but quite restrictive from the deposit inclusion perspective
[2026-05-08T08:27:28.588000+00:00] barnabasbusa: with 16 we can easily see someone just hogging the queue all the time
[2026-05-08T08:27:42.523000+00:00] barnabasbusa: with 512 the hogging becomes a lot more expensive
[2026-05-08T08:28:25.356000+00:00] m.kalinin: are we fine with 512 sig verifications per block?
[2026-05-08T08:36:38.862000+00:00] barnabasbusa: but if we end up limiting all deposit types on the EL side, then that could solve the same issues
[2026-05-08T08:37:38.857000+00:00] barnabasbusa: if we say on the EL side we want at most 512 deposits bundled in a deposit, then we don't need a queue, don't need to worry about top up deposits reaching 8193+, or how much time it would take for signature verification to take place and the mempool could take care of the priority of such deposits
```

</details>

