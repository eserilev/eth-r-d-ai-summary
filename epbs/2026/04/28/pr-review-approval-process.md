# PR review for consensus specs changes

**Channel:** epbs | **Date:** 2026-04-28

## Summary

Potuz requested review of a consensus specs pull request (#5183) that contains changes different from those in the associated EIP, asking for agreement on the modifications so the EIP can be updated accordingly. 

nc1234 questioned whether the payload by range reqresp should also be removed from the changes. Potuz confirmed wanting to remove it but indicated limited time investment in the EIP due to strong pushback from other team members, with a breakout session scheduled to make decisions on the direction forward.

The discussion leaves the PR pending review and the payload by range removal as an open item, with continued devnet-0 work planned until the breakout session resolves the disagreements.

## Participants

- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-28T11:26:12.436000+00:00] potuz: Can you guys please take a look at this? https://github.com/ethereum/consensus-specs/pull/5183
cc <@1126229231572094976> This is different than your changes in the EIP, tell me if you agree with these so that we fix the EIP
[2026-04-28T13:32:46.618000+00:00] nc1234: don't you want to remove payload by range reqresp too?
[2026-04-28T13:54:12.107000+00:00] potuz: I do, but this is as much time as I will give to this EIP for now, there was a strong push back from <@449019668296892420> and <@795439202732867594> and there will be a breakout tomorrow to decide, it's better not to waste time and continue with devnet-0 until then
```

</details>
