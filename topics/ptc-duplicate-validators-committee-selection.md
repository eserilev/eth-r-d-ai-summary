# PTC Duplicate Validators and Committee Selection

---

## 2026-05-06 (epbs)

The discussion centers on whether duplicate validators should be allowed in PTC (Payload Timeliness Committee) selection. Initially there was confusion about the current specification, with jtraglia correcting his previous understanding from an interop discussion. The team confirmed that duplicate validators are indeed possible and allowed in the current spec due to the balance-weighted selection algorithm that wraps around when filling the required 512 committee positions, particularly when random dice throws don't hit the balance threshold.

While theoretically possible on mainnet (if all dice throws were 0), duplicate validators would be extremely unlikely in practice but could occur more readily on smaller chains and devnets. The discussion touched on related concerns about gossip rules for payload attestation messages and the PTC payload reorg mechanism, which led to increasing the PTC size to 16 for testing purposes. The consensus reached was that duplicate members, while primarily relevant for small devnets, don't pose a consensus danger to the protocol.

**Participants:** jtraglia, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-06T19:15:04.819000+00:00] jtraglia: <@755590043632140352> should duplicate validators in a PTC be allowed?
[2026-05-06T20:05:10.861000+00:00] jtraglia: We chatted about this last year, but I just want to double check.
[2026-05-06T20:19:49.134000+00:00] nflaig: but it shouldn't be possible right? or is this rather a question if it should be possible?
[2026-05-06T20:22:48.597000+00:00] potuz: it can be possible in smaller chains, and it is allowed as far as I can tell
[2026-05-06T20:23:15.639000+00:00] jtraglia: I believe what I told you at interop was incorrect. I was thinking of the old spec.
[2026-05-06T20:23:18.718000+00:00] nflaig: I don't believe it is, we derive it from the beacon committee
[2026-05-06T20:23:30.184000+00:00] potuz: I thought we do wrap around when the random balance weight is not hit
[2026-05-06T20:23:35.859000+00:00] potuz: I may be wrong, AFK to check now
[2026-05-06T20:23:44.955000+00:00] jtraglia: No that's right.
[2026-05-06T20:23:47.399000+00:00] potuz: but the check is balance weighted, and I think we always need to fill 512
[2026-05-06T20:23:55.022000+00:00] potuz: so we must wrap around, otherwise it's a bug
[2026-05-06T20:24:15.304000+00:00] jtraglia: Yup. That's what we do. See: https://ethspec.tools/#specs/nightly/functions-compute_balance_weighted_selection
[2026-05-06T20:24:37.855000+00:00] jtraglia: <@1019999229151821936> is the one that corrected my misunderstanding.
[2026-05-06T20:24:39.607000+00:00] potuz: So in principle if all dice throws are 0 then it could happen in mainnet
[2026-05-06T20:24:47.648000+00:00] potuz: but it won't ever happen
[2026-05-06T20:24:58.115000+00:00] jtraglia: Yup. Unlikely but possible.
[2026-05-06T20:26:00.250000+00:00] nflaig: ah so the concern that <@534934855113506836> and I raised was valid I guess, we thought about adding a committee position to the ptc message
[2026-05-06T20:27:02.940000+00:00] potuz: what is the concern?
[2026-05-06T20:33:04.476000+00:00] nflaig: we were looking into the ptc payload reorg mechanism, the concern is this gossip rule
```
_[IGNORE]_ The `payload_attestation_message` is the first valid message
  received from the validator with index
  `payload_attestation_message.validator_index`.
```
there might be something else I can't remember right now, maybe <@534934855113506836> does, but we ended up bumping the ptc size to 16, it was mostly relevant for testing honest payload reorgs

but the duplicate ptc members is really only relevant for small devnets
[2026-05-06T20:40:03.584000+00:00] potuz: right, duplicate members is really mostly about small devnets, but even then it's not an issue IMO
[2026-05-06T20:40:27.247000+00:00] potuz: at least not a consensus danger as far as I can see
```

</details>

