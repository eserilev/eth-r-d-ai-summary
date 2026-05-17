# Builder attack vectors and payload validation

---

## 2026-04-10 (epbs)

The discussion focuses on potential attack vectors where builders could manipulate payload validation by sending valid requests to the majority of validators but invalid ones to a minority (including the proposer). This could trigger ex-ante reorganizations where different parts of the network see different versions of the payload validity.

The participants identify that gossip rules requiring matching request roots with bids should provide protection against this attack, as invalid payloads wouldn't propagate across the network. They note that committed but invalid requests would be treated as no-operations. While this attack vector could still cause some ex-ante reorgs if proposers receive invalid payloads first, the consensus is that this doesn't worsen the current situation since similar payload equivocation issues already exist in the current fork choice mechanism.

The group concludes that these potential reorgs are less severe than current ex-ante reorgs because they wouldn't result in payload theft, and emphasizes the importance of ensuring the Payload Transaction Committee (PTC) votes on valid requests being present.

**Participants:** potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-10T13:49:02.641000+00:00] potuz: So one worry I can see right now is that the builder could cause an ex-ante reorg by sending valid requests to the majority of the chain, invalid ones to a minority and the proposer being among the minority. But these sort of issues already exist today I believe on payload equivocation.  So we need to make sure that PTC votes on the valid requests being there.
[2026-04-10T13:56:05.980000+00:00] tbenr: I see gossip rules to reject non-matching requests root with the bid. should it be enough? committed but invalid requests are noop correct?
[2026-04-10T13:58:37.553000+00:00] potuz: Yeah in principle this prevents the payload from propagating over the chain, and if the proposer gets the invalid one first, it will attempt to reorg it whikle the people that got the valid one first will take it as valid, these sort of ex-ante reorgs are already in the current forkchoice so this does not make the situation worse I think. They aren't as bad as the current ex-ante reorg cause they won't steal a payload.
```

</details>

