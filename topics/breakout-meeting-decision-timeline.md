# Scheduling breakout meeting and decision timeline

---

## 2026-04-10 (epbs)

The discussion centers around scheduling a breakout meeting to decide on a consensus specification change (PR #5094) before the upcoming interop. Potuz emphasizes that while the change is minor from a consensus perspective and simplifies implementation, it involves trade-offs including increased bandwidth usage and complications with proposing during reorgs, similar to previous withdrawal caching issues. The change would require caching requests and recovery mechanisms for reorg situations, though the envelope structure should help by storing data in the database.

There's urgency around the decision because it's creating implementation conflicts - nc1234's team is holding off on devnet0/devnet1 improvements that would become obsolete if the PR is accepted, while potuz's team has already implemented sync functionality that would need significant rollback. The group discusses whether the ACDT breakout session on Monday would be sufficient for decision-making, though timing challenges are noted for some participants like Lighthouse.

The goal is to at least understand all problems and trade-offs by Monday's meeting, even if a final decision isn't reached then. The change could potentially delay devnet 2 but shouldn't impact devnet 1, allowing for parallel decision-making during testing phases.

**Participants:** jtraglia, nc1234, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-10T19:43:43.657000+00:00] potuz: <@539495253418180618> <@592004585506340885> could we schedule a breakout to discuss the above before interop? Do we have enough people that are informed of the change to make a decision or at least to put us in a place so that we decide immediately in the next meeting/interop? The change is really minor from a consensus perspective and it does simplify implementation. There's a trade-off on bandwidth and perhaps some complications on proposing (requests need to be cashed and recovered in case of reorgs). We started analyzing internally
[2026-04-10T19:45:17.973000+00:00] potuz: It may delay devnet 2 but not devnet 1 IMO so it can be decided in parallel to testing
[2026-04-10T19:49:10.068000+00:00] potuz: The reorg situation is one of the complications I could find. It puts us back into what we had for withdrawals that we ended up caching in the state to avoid bugs. But this time is not so bad cause the envelope should have them and we save those in DB
[2026-04-10T19:58:55.701000+00:00] jtraglia: Is the breakout portion of ACDT not sufficient for this?
[2026-04-10T19:59:42.020000+00:00] potuz: I think so if people come informed and actually come on Monday. But the timing typically sucks for lighthouse
[2026-04-10T20:00:35.024000+00:00] jtraglia: The timing will always suck for someone 😢
[2026-04-10T20:01:03.493000+00:00] jtraglia: I agree, people should come prepared.
[2026-04-10T20:03:12.037000+00:00] jtraglia: The goal is to make a decision on this PR, correct?
https://github.com/ethereum/consensus-specs/pull/5094
[2026-04-10T20:17:43.826000+00:00] nc1234: Yes, we are holding off improving our devnet0/devnet1 codebase because some of the improvement will become obsolete if this PR is agreed by clients
[2026-04-10T21:00:13.966000+00:00] potuz: The PR itself requires review, but yes, perhaps not deciding Monday but at least understand what the problems are and what all tradeoffs are
[2026-04-10T21:01:25.429000+00:00] potuz: We're in the other end of the spectrum, we have all of sync already implemented and we will have to roll back a lot because of this, but doing it earlier is better than later if people decide to go with this
```

</details>

