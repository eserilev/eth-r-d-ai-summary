# Proposer Boost Root Update and Network Compatibility Issues

**Channel:** epbs | **Date:** 2026-01-06

## Summary

The discussion centers around a proposed change to the proposer boost mechanism (`update_proposer_boost_root`) that would only update the proposer boost root for blocks from the expected proposer. The main concern raised is network compatibility - if a significant portion of the network runs older clients without this update, there could be a split where some nodes give proposer boost to certain blocks while others don't, potentially causing consensus issues.

The participants debate whether this change requires network-wide coordination or can be implemented as a backward-compatible phase0 change versus waiting for the gloas fork. While potuz initially suggests the change only involves equivocations, fradamt clarifies it affects blocks from divergent branches that aren't equivocations. They discuss potential attack vectors where old blocks could be strategically released to exploit the network split, though fradamt notes similar timing-based attacks already exist in the current system.

The discussion concludes without a firm decision on timing. potuz believes the coordination concerns are mostly theoretical and the change doesn't require synchronization, while nc1234 expresses concern about backward incompatibility. The group agrees the change isn't urgent and could easily be moved to the gloas fork if needed, with the final decision left to Francesco (fradamt) and potuz.

## Participants

- fradamt
- jtraglia
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-06T03:15:11.709000+00:00] nc1234: Regarding the addition of `update_proposer_boost_root`, I wonder what happen when a sizeable portion of the network is running older release of a client or just any client that hasn't updated their proposer boost logic? So if someone publishes a random valid block on a non-canonical chain, a portion of the network will give it proposer boost, but the rest will not, any significant consequence to this?
[2026-01-06T12:13:21.386000+00:00] potuz: why is this? the changes to proposer boost only involve equivocations.
[2026-01-06T15:40:03.724000+00:00] fradamt: There is the phase0 change of only updating proposer boost root for blocks from the expected proposer. We could not do that change retroactively in phase0, and just do it in the gloas spec, so there's no issue with clients doing different things
[2026-01-06T15:41:57.187000+00:00] nc1234: So can I implement that phase0 change for fulu now or I need to hold that off until gloas is live?
[2026-01-06T15:45:13.952000+00:00] potuz: how is this not on an equivocation?
[2026-01-06T15:46:17.752000+00:00] fradamt: If the block is not from the expected proposer, it is not an equivocation, it's just a block from a divergent branch
[2026-01-06T15:46:45.441000+00:00] potuz: yeah but we can't sync it until we know it's the expected proposer in the branch
[2026-01-06T15:46:52.569000+00:00] potuz: let me check the change
[2026-01-06T15:47:13.392000+00:00] fradamt: it is the expected proposer in its own branch, just not on the canonical branch
[2026-01-06T15:47:38.240000+00:00] potuz: ahh this is head_state that you're comparing now
[2026-01-06T15:47:58.876000+00:00] potuz: yeah, that may cause some issue if a large percentage of the chain is not updated and we aren't finalizing
[2026-01-06T15:48:05.293000+00:00] potuz: and forked
[2026-01-06T15:48:40.071000+00:00] fradamt: even if we are finalizing, it can be an attack, just extending a block from epoch N in epoch N+2, before epoch N+1 is finalized
[2026-01-06T15:50:38.641000+00:00] potuz: Yeah, I keep saying that we should ignore blocks not descending from justified checkpoint on gossip and only sync them on RPC
[2026-01-06T15:51:17.919000+00:00] potuz: But if we're finalizing I think that attack doesn't exist just by numbers actually
[2026-01-06T15:52:16.178000+00:00] potuz: I will need to run the numbers, but it's hard to be finalizing regularly, that is the start of N+1 is already justified in N+2 and the start of N be finalized, and then have a branch that 40% of the committee will be larger than the remaining onchain vote on the canonical head
[2026-01-06T15:53:01.702000+00:00] potuz: anyway, casing this on a fork seems ok to me, we have the block when we are applying this so I don't mind adding this extra check.
[2026-01-06T15:53:13.231000+00:00] potuz: Also don't mind leaving it at phase0
[2026-01-06T15:53:19.036000+00:00] fradamt: All in all I think the same kind of attack can apply regardless, e.g. in the status quo you can release such an old block in such a way that half of the network sees it before the real block and half sees it after, or something like that. It takes a bit more work but ends up being equivalent to 50% of the network running an updated client and releasing the block early (half will set proposer boost root, half won't)
[2026-01-06T15:54:15.362000+00:00] fradamt: so idk, I think I'd go with whatever is easiest
[2026-01-06T15:57:09.371000+00:00] nc1234: <@592004585506340885> Sounds like this is backward incompatible. Any concern about leaving this at phase0? Or should we revert the change from phase0-fulu and all change should be in gloas spec?
[2026-01-06T15:59:41.838000+00:00] potuz: A comment on this that is not directly related. But PB was included in principle to prevent staircase attacks. But if we are split like this for long enough that the shuffle is different in both branches. Doesn't this rule remove entirely PB effect with regards to this attack? That is. In the short term it helps prevent setting it up (although we know it doesn't much) but if it's already split for whatever reason, PB won't help fix it right?
[2026-01-06T16:00:29.221000+00:00] potuz: We have plenty of phase0 backported changes in fork choice, what's the problem?
[2026-01-06T16:04:16.862000+00:00] nc1234: Usually backported changes won't cause issue to the network and thus clients can implement and release the change any time they want. But this change can cause issue, so we need some synchronization
[2026-01-06T16:05:03.704000+00:00] potuz: I don't think this requires synchronization at all
[2026-01-06T16:05:12.211000+00:00] potuz: I think this is only theoretical
[2026-01-06T16:05:42.312000+00:00] potuz: But it's easy to case it to Gloas so I don't mind
[2026-01-06T16:05:55.459000+00:00] potuz: And it's not urgent at all to ship it either
[2026-01-06T16:08:21.001000+00:00] jtraglia: Yeah I'm not 100% sure if this requires coordination or not. I don't believe it does. I'll leave it up to Francesco & Potuz.
```

</details>
