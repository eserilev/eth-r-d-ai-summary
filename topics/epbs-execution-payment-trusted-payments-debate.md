# ePBS execution_payment field and trusted payments debate

---

## 2026-04-28 (epbs)

The discussion centers around a controversial `execution_payment` field proposed in PR #4733 for ePBS (enshrined Proposer-Builder Separation). The field is intended to support trusted payments, where validators receive payment through transactions rather than withdrawals. Two main reasons were given for this approach: it would make accounting easier for Lido (compared to receiving ETH via withdrawals), and it could handle edge cases where builders exploit blocks for large amounts (like 300 ETH) that they cannot pay upfront.

Several participants expressed strong skepticism about including trusted payments in the protocol at all. m.kalinin questioned whether the field provides any protocol-level value and suggested it could be handled outside the bid structure entirely. nflaig revealed that while Lido appears to favor trusted payments, RocketPool does not want to use them, raising concerns that ~30% of the network (presumably Lido's stake) would rely on trusted payments. There are also potential liveness concerns with trustless payment bids over P2P networks.

The discussion remains unresolved with fundamental disagreement about whether trusted payments should be supported in ePBS. nflaig has an open PR to remove trustless payments entirely, suggesting the debate reflects deeper architectural questions about payment mechanisms in the new protocol design.

**Participants:** m.kalinin, nflaig, potuz

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-29 (epbs)

The discussion centers on whether trusted payments should be allowed in the ePBS (enshrined Proposer-Builder Separation) protocol. fradamt references extensive prior discussions with both internal and external parties that resulted in a decision to allow trusted payments, emphasizing this shouldn't be reconsidered without strong justification.

There appears to be consensus on keeping trusted payments available at the protocol level while leaving implementation flexibility to individual clients. nflaig agrees with maintaining this option based on conversations with various stakeholders, noting that default client behavior can be determined separately from protocol capabilities.

For client implementation, jtraglia suggests there doesn't need to be a standardized default behavior across clients, proposing that some clients could exclusively support trustless (p2p) bids while others might require explicit opt-in via flags like `--allow-dangerous-trusted-payments` to enable trusted payment configurations.

**Participants:** fradamt, jtraglia, nflaig

<details>
<summary>Raw messages</summary>

```
[2026-04-29T07:41:06.914000+00:00] fradamt: We discussed this a lot months ago here and in breakouts (including with external parties) and the decision was that they should be allowed. I don't think this should be renegotiated lightly
[2026-04-29T08:17:25.657000+00:00] nflaig: yes agree, talked with a bunch of people and it might be a good idea to keep the door open to have trusted payments, at least from the protocol perspective and we can still think about what the default behavior should be for clients
[2026-04-29T08:23:57.140000+00:00] jtraglia: Re trusted payments in clients, I don't think there needs to be a default behavior. I think it's perfectly acceptable to say X client will only allow trustless (p2p) bids. One fun idea would be a required `--allow-dangerous-trusted-payments` flag (or something like that) in order to configure non-p2p bids.
```

</details>

