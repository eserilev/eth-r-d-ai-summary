# Payload Invalidation vs Raising Limits Strategy

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on strategies for handling payload invalidation versus raising SSZ limits in Ethereum. Potuz argues that invalidating payloads is worse than the alternative because it offers no recovery chance unless builders avoid packing problematic transactions. With the current approach, recovery depends on the next proposer not reorganizing the block, which should work given the honest reorg feature that only reorgs one slot. While the first block would be reorged and the second would take seconds to execute, the third proposer should build on the previous block, allowing the system to eventually heal. However, Potuz notes the attack vector can be continuously repeated.

Jtraglia proposes that execution layer clients should be smart enough to avoid creating payloads exceeding SSZ limits and outlines three potential solutions: (1) queue deposits and signature verifications while raising the SSZ limit, (2) lower the SSZ limit and make ELs aware, or (3) simply raise the SSZ limit and accept the risk since attacks are expensive. The discussion also touches on gas limits, with jtraglia noting that 200m gas is the floor with plans to reach 400m gas in the near future.

An open question remains about recovery time in kurtosis devnets when handling 8192 new builder deposits, which jtraglia specifically asks about but receives no answer in this discussion thread.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T00:52:16.978000+00:00] potuz: Because invalidating the Payload seems to be even worse
[2026-05-08T00:52:53.520000+00:00] potuz: Then there's not even a chance of recovering unless a builder doesn't pack the txs
[2026-05-08T00:53:35.183000+00:00] potuz: With this we only need the next proposer not to reorg the block
[2026-05-08T00:54:00.802000+00:00] potuz: Which I think will happen if we stick to the current honest reorg feature of only reorging one slot
[2026-05-08T00:54:17.643000+00:00] potuz: So the first block will be reorged but the second one won't
[2026-05-08T00:54:40.215000+00:00] potuz: Will take seconds to execute but the third proposer should build on the previous block
[2026-05-08T00:54:48.629000+00:00] potuz: So I think we should eventually heal
[2026-05-08T00:54:59.766000+00:00] potuz: The problem is that the attack can be always continued
[2026-05-08T00:56:57.326000+00:00] jtraglia: Surely ELs can just be smart enough to not create a payload with more than the SSZ limit.
[2026-05-08T00:57:38.921000+00:00] jtraglia: I feel like there are two options: (1) queue deposits and sig verifications + raise the SSZ limit or (2) lower the SSZ limit and make ELs aware of this.
[2026-05-08T00:57:59.268000+00:00] jtraglia: But you are saying: (3) just raise the SSZ limit and hope it's not an issue because it's expensive.
[2026-05-08T01:00:05.867000+00:00] jtraglia: Isn't 200m gas limit the _floor_? I thought we were shooting for like 400m gas in the near future.
[2026-05-08T01:01:26.338000+00:00] jtraglia: <@808969530608451584> what happens in your kurtosis devnet when there are 8192 new builder deposits? How long does it take to recover?
```

</details>
