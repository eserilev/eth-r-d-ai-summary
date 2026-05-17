# Proposer Boost and Builder Unblinding Implementation

---

## 2026-05-12 (epbs)

Potuz raised concerns about implementing proposer boost alongside the builder unblinding feature, specifically regarding how it might weaken the existing phase 0 validation clause that ignores blocks after the first valid block from a proposer in a slot. The core issue is that tracking equivocations over gossip would require validating signatures on potentially malicious data received after the first block, and checking for differences between repeated messages (though libp2p already filters messages with identical hashes, requiring hash tree root validation to detect differences).

Potuz is seeking guidance on the best implementation approach, noting that the naive solution would involve validating equivocations only during an early window for the current slot and caching a boolean flag when detecting valid signature equivocations. The discussion appears to be ongoing with no clear resolution provided yet, as Potuz explicitly states uncertainty about how to proceed with this implementation challenge.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-12T23:58:29.016000+00:00] potuz: <@520034910149410861> (and cc <@363800010518822915> that is suffering with this on spec tests and <@504202741933932544> that may have already implemented this). I am struggling to see how to deal with proposer boost and the builder unblinding feature you added Francesco. In particular I am worried about weakening the phase 0 clause:
```
    # [IGNORE] The block is the first block with valid signature received for the proposer for the slot
    if (block.proposer_index, block.slot) in seen.proposer_slots:
        raise GossipIgnore("block is not the first valid block for this proposer and slot")
```
Keeping track of equivocations seen over gossip will require us to validate signatures on possible garbage that may arrive after we've already seen the first block. And moreover will require us to check on every repeated message for differences in the block. Most of the latter problem is probably already covered by libp2p that will not even let through messages with the same hash, but still we would be required to validate the HTR and check it's different. 

I'm just asking for advice since I am not sure how to proceed. There does not seem to be any approach better than the naive one of simply validate these during the early window (only early equivocations and for the current slot matter) and just cache a boolean if we catch one with a valid signature.
```

</details>

---

## 2026-05-13 (epbs)

The discussion focuses on equivocation detection mechanisms and proposer boost mechanics in Ethereum's consensus layer, particularly around protecting builders from malicious proposers. The core attack vector involves a proposer revealing two blocks for the same slot after a builder has already revealed their payload based on seeing 40% support, then using proposer boost on a subsequent self-built block to reorg out the builder's payload.

The conversation centers on understanding the timeliness rule for equivocation detection, which only considers "early" equivocations when determining whether to apply proposer boost. This rule is designed to prevent attackers from removing proposer boost from honest proposers by releasing late equivocations, while ensuring that builders are protected when they reveal based on timely blocks with sufficient support. The logic works by checking if other blocks from the same proposer were published in a timely manner - if so, proposer boost is not applied to late equivocating blocks.

Several implementation details were clarified, including the need for better documentation and tests since the logic is confusing and was initially implemented incorrectly. The group reached consensus on the rationale behind the timeliness rule after some initial confusion about how the equivocation detection check works. Action items include adding comments to the `should_apply_proposer_boost` function and creating comprehensive tests for both equivocation scenarios.

**Participants:** fradamt, m.kalinin, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-13T08:24:09.853000+00:00] m.kalinin: You will have the equivocal parent block in the `Store` after processing the child, won't you? You never accept equivocal blocks on p2p, but you may pull one via RPC
[2026-05-13T08:27:51.267000+00:00] m.kalinin: The attack we protect a builder from is the following:
1) slot N: Builder sees 40% support on a beacon block with the bid and reveals
2) slot N: Proposer reveals two blocks for the same slot, the second can be self built; attesters are split
3) slot N+1: Proposer belongs to the same entity and self-builds a new block with the builder's content and publishes a timely block
4) builder's payload is reorged out cause it's beacon block has 40% and the competing slot N+1 block has 40% via proposer boost + a few attestations from the previous slot attesting to its parent

is the understanding above correct?
[2026-05-13T08:45:56.682000+00:00] m.kalinin: Oh, i also see `store.block_timeliness[root][PTC_TIMELINESS_INDEX]`, but what if proposer at the previous slot reveals that block after that deadline and also atts in the support of that equivocal block that it has produced. What is the reason to count equivocal blocks only if they are published before `store.block_timeliness[root][PTC_TIMELINESS_INDEX]`?
[2026-05-13T12:05:52.551000+00:00] fradamt: > Keeping track of equivocations seen over gossip will require us to validate signatures on possible garbage that may arrive after we've already seen the first block.
Why is this different than doing so before seeing the first block? It's still punishable if someone sends a message that doesn't verify

> And moreover will require us to check on every repeated message for differences in the block
This would only have to be done once, once a single equivocation has been seen we can go back to ignoring everything
[2026-05-13T12:09:50.016000+00:00] fradamt: That's mostly right, except:
2) The proposer would reveal the second block afterwards, once the builder has already published the payload (so it can't just detect the equivocation and withhold)

It doesn't need to publish the equivocating block on time because it doesn't care about receiving any attestation weight, since it can just get the 40% from proposer boost + a few of its own attestations.

I don't know how to solve this without equivocation detection. Still up for doing away with trustless payments though 😄
[2026-05-13T12:11:34.142000+00:00] m.kalinin: Why do we need `store.block_timeliness[root][PTC_TIMELINESS_INDEX]`?
[2026-05-13T12:25:09.808000+00:00] potuz: yep, I am now implementing exactly this, my question was vacuous anyway since today we anyway do that extra signature verification to prepare a proposer slashing on the fly.
[2026-05-13T12:26:45.792000+00:00] potuz: This is because proposer boost only cares about "early" equivocations. Francesco argues that otherwise you can remove proposer boost from an honest proposer by releasing late equivocations. This is mostly an edge case that I am not sure justifies tracking these timeliness in the store, but anyway it's fairly trivial to implement so no problem
[2026-05-13T12:27:14.591000+00:00] fradamt: I guess the "ignore after second message" rule should be specified in the executable p2p spec?
[2026-05-13T12:30:30.074000+00:00] m.kalinin: this timeliness rule seems to be gamable if you have two consecutive proposals. Then you can have proposer boost for your malicious proposal build on top of an equivocal block with empty payload and stolen builder's payload
[2026-05-13T12:30:52.849000+00:00] potuz: that is also possible today
[2026-05-13T12:33:34.493000+00:00] m.kalinin: i am saying that timeliness rule seems to make things worse. it is removing PB from honest proposer at the cost of slashing vs having PB on malicious proposal and helping it win when it makes sense to be slashed as the fee from stolen payload covers the costs.
[2026-05-13T12:34:04.164000+00:00] m.kalinin: but i might be wrong
[2026-05-13T12:34:13.923000+00:00] potuz: I don't understand, today proposer boost is unconditionally applied
[2026-05-13T12:34:20.998000+00:00] potuz: regardless of equivocations
[2026-05-13T12:35:22.970000+00:00] m.kalinin: it's not about today vs Gloas. we change conditions for applying proposer boost and we introduce a new rule for early equivocations while removing "early" would be a net positive to me. Why not remove it then?
[2026-05-13T12:42:09.844000+00:00] fradamt: Ah no you could not do this, because *the timely block that caused the builder to release the payload* would then be detected as an equivocation:
- Let `B` be the "good block" that caused the builder to release its payload. 
- `B` must have been released early, so everyone would have recorded `B`. 
- Then the proposer releases `B'` late and builds on it, but everyone that gets it sees that `B` and `B'` are an equivocating pair, because they are evaluating `B'` and they have `B` as a timely recorded sibling:

```
    equivocations = [
        root
        for root, block in store.blocks.items()
        if (
            store.block_timeliness[root][PTC_TIMELINESS_INDEX]
            and block.proposer_index == parent.proposer_index
            and block.slot + 1 == slot
            and root != parent_root
        )
    ]
```

If the proposer had built on `B`, `B'` would not be detected as a timely equivocation (because it is not, it is late), and proposer boost would have applied, because `equivocations` would be empty. If the proposer maliciously builds on `B'` (even though it has no weight), then we have `equivocations = [B]`, because `B` was timely
[2026-05-13T12:44:03.202000+00:00] fradamt: It's this statement (from https://github.com/ethereum/consensus-specs/pull/4807):

> Assuming < 20% adversary, we get builder reveal safety:
> Builder reveals when seeing block B with > 40% weight and no equivocations
> => if the proposer extends an equivocation B', proposer boost doesn't apply to B': B' is weak because it is late and thus does not accrue honest weight, and attesters detect the existence of an equivocation (B is timely).
> => no matter what the proposer does, the block stays canonical
[2026-05-13T12:46:10.155000+00:00] potuz: btw we would need tests for this, my initial implementation for this actually only checked for the existence of equivocations symmetrically and would have counted equivocations both ways, only after you linked to the above conversation that I changed it to track differently
[2026-05-13T12:49:46.687000+00:00] m.kalinin: oh, i see now, sorry i got confused by that check. So, it checks that *another* block is timely
[2026-05-13T12:49:56.228000+00:00] m.kalinin: then it makes sense!
[2026-05-13T12:55:16.106000+00:00] fradamt: It is confusing 😂
[2026-05-13T12:56:32.887000+00:00] fradamt: (every time this comes up I never remember about the details and I am just re-reading the description of the PR and asking claude questions about it before answering 😄)
[2026-05-13T13:12:19.058000+00:00] nflaig: that's also me trying to understand your fork choice PRs 😂
[2026-05-13T13:23:28.110000+00:00] potuz: actually a comment on `should_apply_proposer_boost` would be good, most probably I wasn't the only one that implemented this incorrectly. Although a test for both scenarios would be needed anyway
```

</details>

---

## 2026-05-15 (epbs)

The discussion centers on when builders should reveal their payloads in the Ethereum consensus mechanism. jtraglia initially asked whether builders should reveal payloads only after seeing 66%+ attestations supporting a block, and whether they should withhold if insufficient attestations are present by the `ATTESTATION_DUE_BPS_GLOAS` deadline. potuz clarified that honest behavior should be to submit as soon as they see the block, though builders may choose more sophisticated strategies.

The conversation revealed confusion about threshold percentages, with jtraglia questioning a 40% figure from a GitHub PR versus the expected 66%. potuz explained that the 40% threshold derives from proposer boost mechanics, allowing builders to safely reveal when they see 40% attestations rather than waiting for 66%. However, potuz identified that parts of the current specification appear to be outdated artifacts from early 2024 drafts.

The discussion concluded with potuz noting that references to "payload withheld messages" in the current master branch specification are likely obsolete remnants from earlier versions, suggesting the main specification text was updated but section titles weren't properly maintained. This indicates a need to clean up outdated specification language.

**Participants:** jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-15T18:40:48.029000+00:00] jtraglia: I have a dumb question. When exactly should a builder reveal their payload? My understanding is that they should reveal as soon as they see that 66%+ of attestations support the block. If at `ATTESTATION_DUE_BPS_GLOAS` they do not see enough attestations for the block, they should decide not to reveal the payload. Is this accurate?
[2026-05-15T19:25:29.446000+00:00] potuz: The honest behavior is to submit as soon as they see the block, if they want to be fancy so be it, but the spec should say that they need to reveal when they see a block
[2026-05-15T19:27:33.687000+00:00] jtraglia: But not always. The spec does say this:
[2026-05-15T19:29:14.571000+00:00] jtraglia: I don't fully understand where the 40% number [in this PR](https://github.com/ethereum/consensus-specs/pull/4807) comes from:
[2026-05-15T19:29:16.625000+00:00] jtraglia: 
[2026-05-15T19:32:49.403000+00:00] potuz: *may* is keyword here, and I think that spec also may be very old, from where are you taking this? "payload withheld mesages" sounds like an artifact of my first spec circa 2024
[2026-05-15T19:33:25.489000+00:00] potuz: that 40% is from proposer boost
[2026-05-15T19:34:46.829000+00:00] jtraglia: That's from here, currently in master:
https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/builder.md#honest-payload-withheld-messages
[2026-05-15T19:35:36.995000+00:00] jtraglia: And hmm. Okay so a builder can safely reveal when they see 40% of attestations in favor of the block? Not 66%.
[2026-05-15T21:40:40.899000+00:00] potuz: That is definitely an artifact from the early draft, withheld message doesn't make any sense anymore. Probably the main text was changed and the title of the section was maintained
```

</details>

---

## 2026-05-16 (epbs)

The discussion centers on determining safe attestation thresholds for builder payload publishing to prevent same-slot unbundling attacks. The conversation explores various threshold percentages, with initial confusion between forkchoice and finalization thresholds. Nero_eth suggests that builders need more than 50% of votes to avoid being unbundled by competing equivocations, but later considers higher thresholds like 70-90% for additional safety.

The group converges on a key insight: builders can achieve safety by waiting for both the attestation deadline and seeing attestations for their block commitment without early equivocations. Potuz clarifies that 40% attestations with no equivocations provides sufficient protection against a 20% attacker, explaining the math where honest chains have 80% support versus attackers' maximum 60%. Terencechain notes that low-carb-crusader attacks aren't applicable here, and the main risk is early revelation of transactions rather than same-slot unbundling.

The discussion concludes with agreement that the specification should simply state "release if you see a block containing your bid" while leaving implementation-specific safety thresholds to individual builders. However, an open question remains about the precise definition of "early equivocations" and whether the 40% threshold combined with waiting past the attestation deadline provides the 100% certainty that builders are seeking against same-slot unbundling.

**Participants:** jtraglia, nero_eth, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-16T05:29:09.828000+00:00] nero_eth: Why 40, or 66? To not be unbundled in the same slot you need more attestations than a competing equivocation. Wouldn't that mean >50% of votes? Or any majority of votes while >50% votes (independently if they voted for the proposers block or the parent)?
[2026-05-16T05:32:02.809000+00:00] nero_eth: If we put the attestation deadline to 2s, and attesters attest as early as they see the block, then, for the EL repricing, we can assume we have the full time from attestation deadline to the payload deadline for propagation. The earlier we allow builders to safely publish (safe as in "not risking same-slot unbundling") the better.
[2026-05-16T12:51:36.461000+00:00] jtraglia: Regarding 66, I might be confusing thresholds for forkchoice with finalization 😅
[2026-05-16T13:02:50.203000+00:00] nero_eth: Tbf, just saying 50% is simplifying things too and is neglecting that attesters could equivocate too.
I think 70%, similar to the floor of FCR, or 90% (with 20% adversary) might be the right number here (using `Q > 1/2(1+ boost/committee) + beta`). At this point, the block can be fast-confirmed and should be safe (at least in the happy path when we finalize). My feeling is, 90% is overly conservative here, but unsure what one would recommend to builders.
cc <@520034910149410861> <@425572898787426305>
[2026-05-16T13:30:08.448000+00:00] nero_eth: Thinking about it more, if the builder waits for "a few" attestations and publishes after the attestation deadline, it is already safer than today. E.g. if no equivocating block was seen at the attestation deadline, it won't get boost and since the builder didn't publish yet, there's nothing to be unbundled.
So, publish after seeing the commitment + after the attestation deadline seems fine?!
[2026-05-16T13:56:06.405000+00:00] terencechain: Worth noting low-carb-crusader attack can not happen here, so the only downside is it reveals too early, then gets reorg and future builders learn about its tx. Sensitive txs wold have bounded by its slot anyway
I think ideally it still wants  to reach `BUILDER_PAYMENT_THRESHOLD_NUMERATOR` so it gets paid
[2026-05-16T15:46:31.988000+00:00] potuz: 40% and no equivocations seen, that's enough to guarantee safety against a 20% attacker
[2026-05-16T15:48:31.801000+00:00] potuz: But anyway the spec should not really mention any of this IMO, the spec should say "release if you see a block containing your bid" and leave all these other aspects up to implementations
[2026-05-16T15:50:31.323000+00:00] nero_eth: Agree.
It is 40% and no equivocations **after** the atteststion deadline?
[2026-05-16T15:51:13.086000+00:00] potuz: It's no early equivocation
[2026-05-16T15:52:15.165000+00:00] nero_eth: And early is defined as before attestation deadline?
I assume there's no proposer boost after the atteststion deadline.
[2026-05-16T15:55:23.321000+00:00] nero_eth: I'm just trying to figure out what to tell to builders as this question has come up and they want to protect against same slot unbulding with 100% certainty (e.g. accepting attacks by >20% attackers). 
Is it 40% attestations + after the atteststion deadline - then it's safe to publish?
[2026-05-16T16:07:36.543000+00:00] terencechain: same slot unbunding cant happen right, i thought we are talking about next slot reorg
[2026-05-16T16:13:33.391000+00:00] terencechain: ~~is it 30%? where do i mess up on math?~~
~~i think you want Q + PB > 1 - Q...   => Q > 30%~~
~~Left side is the honest side.. right side is the attacker side~~
[2026-05-16T16:22:22.583000+00:00] terencechain: Nvm.. I see the math now..
Q = 40%
PB = 40%
attacker = 20%
honest chain = 80%
attacker chain most could have = 60%
20% is the different btw honest and attacker chain here
```

</details>

