# Breakout Meeting Scheduling and Decision Timeline

**Channel:** epbs | **Date:** 2026-04-10

## Summary

The discussion centers around scheduling a breakout meeting to evaluate a consensus specification change (PR #5094) before the upcoming interop. Potuz explains that while the change is minor from a consensus perspective and simplifies implementation, it involves trade-offs including increased bandwidth usage and complications around proposing where requests need to be cached and recovered during reorgs. He notes similarities to previous withdrawal handling that required state caching, though this case may be less problematic since envelopes contain the necessary data and are stored in the database.

The timing presents challenges for different teams - some are holding off on improving their devnet0/devnet1 codebase because improvements may become obsolete if the PR is accepted, while others have already implemented sync functionality and would need to roll back significant work. There's consensus that making this decision earlier rather than later is preferable regardless of the outcome.

The group agrees that the ACDT breakout portion could be sufficient for discussion, provided participants come prepared and attend (despite timing challenges for some teams like Lighthouse). The immediate goal is to understand the problems and trade-offs involved, with the actual decision potentially coming after Monday's meeting once proper review of the PR is completed.

## Participants

- jtraglia
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
