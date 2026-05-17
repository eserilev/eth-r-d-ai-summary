# Progressive SSZ Containers and EIP-7688 Implementation

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers on implementing EIP-7688 (Progressive SSZ Containers) in the upcoming Ethereum devnet, with participants debating the timing and technical challenges. Tersec notes that stable SSZ is already queued for implementation once glamsterdam-devnets achieve stability, but warns that waiting longer will constrain feasible changes. When asked about scheduling EIP-7688 for the next devnet in 1-2 weeks, Nimbus reports having a draft implementation ready based on alpha.7, while other client teams express concerns.

The main technical issue raised is how to handle unbounded schema growth with progressive containers. Tbenr suggests implementing practical upper bounds that can be overridden at the type level, potentially making these bounds required when progressive types are involved. Wemeetagain proposes setting sensible limits per topic/object on gossip and request-response protocols as a solution.

Client readiness varies significantly: Nimbus could potentially implement within 1-2 weeks, Teku has concerns about unbounded schemas but might participate if solutions are found, Prysm prefers focusing on fixing existing bugs and P2P/builder API testing before expanding scope, and Grandine lacks the SSZ implementation entirely. The consensus appears to lean toward addressing current stability issues before introducing new features, with no clear agreement on the 2-week timeline.

## Participants

- barnabasbusa
- jtraglia
- potuz
- sauliusgrigaitis
- tbenr
- terencechain
- tersec
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T14:29:35.611000+00:00] tersec: stable SSZ is already enqueued basically, no? as in, the default action is that it happens when glamsterdam-devnets achieve some metric of stability
[2026-05-08T14:30:05.705000+00:00] tersec: tradeoff here is that the later one waits the more structurally constrained the remaining feasible changes will be
[2026-05-08T14:30:09.929000+00:00] jtraglia: Yes, a progressive list will handle this too. But in the meantime, we can set the limit to some large value.
[2026-05-08T14:30:50.080000+00:00] tersec: i.e. if it's early enough more interesting changes might remain testable. the later it gets the more it's going to be, tweak parameters, adds some kludge here, etc
[2026-05-08T14:30:57.784000+00:00] barnabasbusa: when can we have the progressivecontainer implemented by all CLs?
[2026-05-08T14:31:51.379000+00:00] tersec: Nimbus has a draft branch ready already, can't speak for other CLs obviously
[2026-05-08T14:32:09.267000+00:00] barnabasbusa: is it based on top of latest alpha 7?
[2026-05-08T14:32:16.860000+00:00] barnabasbusa: how is the consensus spec pr looking for it?
[2026-05-08T14:32:17.794000+00:00] tersec: yes
[2026-05-08T14:32:29.094000+00:00] tersec: (based on alpha.7)
[2026-05-08T14:32:31.491000+00:00] barnabasbusa: could we realistically schedule it in for the next devnet in 1/2 w?
[2026-05-08T14:33:27.863000+00:00] tersec: for Nimbus definitely for 2 weeks, plausibly for 1 week. for other CLs obviously I couldn't say
[2026-05-08T14:35:02.397000+00:00] tersec: https://github.com/status-im/nimbus-eth2/tree/eip-7688
[2026-05-08T14:36:18.092000+00:00] barnabasbusa: <@592004585506340885> instead of increasing it then removing it again with 7688, we should probably just aim to remove it with 7688?
[2026-05-08T14:37:44.840000+00:00] barnabasbusa: <@358120958726373381> <@363800010518822915> <@586161934425128960> <@504202741933932544> <@777935002263617576> would you be ok to schedule in 7688 in about 2w from now in the following devnet ?
[2026-05-08T14:38:37.052000+00:00] barnabasbusa: In the meantime I'm sure we gonna be finding a bunch of other epbs bugs so obviously those should also be ironed out in the next 2 weeks
[2026-05-08T15:22:15.789000+00:00] tbenr: my problem is that we haven't discussed how to deal with the schema becoming unbounded. If we assume that we will find a solution for that no matter what, i think teku can participate but ATM we can't be fully sure. There are a lot of moving parts for us in merging the interop branch into master.
[2026-05-08T15:29:50.754000+00:00] potuz: This really seemed like a serious issue to me, haven't heard back from anyone on this point
[2026-05-08T15:41:27.546000+00:00] terencechain: I'm not sure. I can ask the team. But my personal vote would be fix and harden the existing code first rather then increasing scope. There's already enough bugs to be fixed. We also should focus on p2p bids and builder api testing before then
[2026-05-08T15:50:41.227000+00:00] wemeetagain: we will just put sensible limits per topic / object on gossip and reqresp
[2026-05-08T15:50:56.753000+00:00] tbenr: My current position is that we could introduce an override (that can be specified on each affected type) which will set a practical upperbound. We can inject this at type level so the thing remains transparent for the machinery using it
[2026-05-08T15:52:52.352000+00:00] tbenr: we could even make it required if a progressive type is involved (so you are forced to declare something and not inherit unbounded size)
[2026-05-08T15:55:17.388000+00:00] sauliusgrigaitis: We don’t have that Ssz implementation, so definitely not in two weeks for us.
```

</details>
