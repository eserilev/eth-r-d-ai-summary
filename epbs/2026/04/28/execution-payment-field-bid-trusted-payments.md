# Execution payment field in bid and trusted payments

**Channel:** epbs | **Date:** 2026-04-28

## Summary

The discussion centers around PR #4733 which proposes adding an `execution_payment` field to bids in Ethereum's consensus specs. The participants debate whether this field should be included in the bid itself or handled separately, with concerns that it appears "completely useless from a protocol perspective." The primary drivers for this change seem to be Lido's preference for trusted payments over trustless payments, citing accounting difficulties and potential scenarios where builders might need to exploit blocks worth 300 ETH without having the funds upfront.

There's significant pushback against allowing trusted payments at all, with nflaig expressing concern that approximately 30% of the network (referring to Lido's stake) might use trusted payments, while other major staking pools like Rocketpool appear to reject this approach. The debate reveals underlying tension about whether the protocol should encourage trustless payments as the standard or accommodate trusted payment flows for major stakeholders.

The discussion references previous All Core Developers (ACD) calls where this was debated, and mentions that trustless payment bids via P2P networking could pose liveness concerns. An alternative proposal exists to remove trusted payments entirely from the specification, though no clear consensus emerges from this conversation about the best path forward.

## Participants

- m.kalinin
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-28T11:02:31.797000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/4733 <@755590043632140352> there is 0 context in this PR why we need `execution_payment` field in the bid, when was this discussed? or is there a writeup from Lido and others that explains why they wanna use trusted payments only, maybe a breakout call to rewatch?
[2026-04-28T11:03:39.172000+00:00] potuz: It was discussed in ACD and those discussions around this give me nightmares, I can dig the ACD recording later, probably easy to search in forkcast.
[2026-04-28T11:05:01.889000+00:00] m.kalinin: what if this value passed aside the bid? is this field being a part of signing data a reason to have it in the bid?
[2026-04-28T11:06:01.565000+00:00] nflaig: the reasons I gathered from talking today in the breakout
- 1) might make accounting for Lido harder
- 2) there might be one block with 300ETH exploit that a builder does for which it cannot upfront the ETH so has to use trusted payment route
[2026-04-28T11:06:51.346000+00:00] nflaig: I have an issue with allowing trusted payments at all from a protocol perspective
[2026-04-28T11:06:58.138000+00:00] m.kalinin: it looks completely useless from the protocol perspective and even aside to the ePBS as it improves (i hope it does) trusted payment flow
[2026-04-28T11:08:22.096000+00:00] m.kalinin: This field can be aside to the bid in the trusted flow API payload and even signed at the cost of one more signature operation, not sure how important it is to avoid such a complexity as it looks like a little thing
[2026-04-28T11:10:16.466000+00:00] m.kalinin: > might make accounting for Lido harder
my other Q would be: is this field reliable for any accounting?
[2026-04-28T11:11:03.142000+00:00] nflaig: no, it's not about that field, it's just one part, I think clients should not allow trusted payments at all
[2026-04-28T11:11:38.743000+00:00] nflaig: my understanding is that if you send the eth via a tx (as today) it keeps it easier for them compared to receiving the eth via a withdrawal
[2026-04-28T11:12:32.227000+00:00] m.kalinin: oh, I see
[2026-04-28T11:12:46.453000+00:00] nflaig: well I am talking to rocketpool right now, and they don't wanna use trusted payments it seems
[2026-04-28T11:13:02.330000+00:00] nflaig: waiting for lido response, but it seems insane to me that ~30% of the network will use trusted payments
[2026-04-28T11:16:29.793000+00:00] potuz: Didn't you have a PR open to not even allow trustless payments? 🙂
[2026-04-28T11:18:15.158000+00:00] nflaig: yes and having the `execution_payment` in the spec was actually one reason that triggered that, if we don't push towards having trustless payments as the standard and don't use it, then just remove it already, also today I learned the trustless payments bids via p2p is a potential liveness concern...
[2026-04-28T11:18:24.035000+00:00] nflaig: https://github.com/nflaig/consensus-specs/pull/1 (happy to rebase an open on the spec 😄 )
```

</details>
