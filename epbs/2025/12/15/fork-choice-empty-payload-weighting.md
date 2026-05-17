# Fork Choice and Empty Payload Weighting

**Channel:** epbs | **Date:** 2025-12-15

## Summary

A community member (hangleang.eth) sought help understanding a specific part of the Ethereum specification related to fork choice and empty payload weighting. They raised a concern about how to weight fork choice nodes when most attesters vote for an empty payload branch, questioning whether this would cause the network to continue building on the empty payload branch due to its higher weight.

Potuz clarified that current slot votes count for both branches since they aren't on contending branches relative to the full branch. He explained that if the next consensus layer (CL) block is missing, subsequent CL attesters will vote for the current CL block on contending branches - and crucially, if they vote for the empty payload, those votes do not count toward the full payload branch. This appears to address the weighting concern, though the technical details suggest this is part of a larger specification that handles edge cases in Ethereum's consensus mechanism.

## Participants

- hangleang.eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T01:50:12.668000+00:00] hangleang.eth: <@&595685821026271242>
[2025-12-15T05:34:27.213000+00:00] hangleang.eth: Re: https://discord.com/channels/595666850260713488/874767108809031740/1449008588797247488

can anyone help us to understand this part of the spec?
[2025-12-15T08:43:28.793000+00:00] potuz: Happy to help but isn't this what you needed <@498331483732312075> ?
[2025-12-15T08:46:06.936000+00:00] hangleang.eth: ohh! I didn't look through the chat. I will check it, thanks
[2025-12-15T08:49:59.393000+00:00] hangleang.eth: <@755590043632140352> sir, but how can we weight the fork choice node? if most of attesters vote for empty payload branch, so it will cont. to build up on the empty payload, since the branch has more weight
[2025-12-15T08:52:14.365000+00:00] potuz: The current slot votes count for both they aren't on a contending branch to the full branch.
[2025-12-15T08:54:26.045000+00:00] potuz: Suppose the next CL block is missing, then the next CL attesters will vote for the current cl block and they yes will vote on contending branches, if they vote for empty those votes do not count for full
```

</details>
