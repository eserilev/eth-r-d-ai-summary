# Equivocation Detection and Proposer Boost Logic

**Channel:** epbs | **Date:** 2026-05-13

## Summary

This discussion centers on equivocation detection logic and proposer boost mechanics in Ethereum's consensus layer, specifically regarding builder protection against malicious proposers. The key attack vector involves a proposer equivocating (creating two blocks for the same slot) after a builder has already revealed their payload based on seeing 40% support, then using proposer boost in the next slot to reorganize out the builder's block while stealing their payload content.

The technical solution involves tracking block timeliness and only removing proposer boost when timely equivocations are detected. The logic checks if another block from the same proposer was published early enough to be considered "timely" - if so, proposer boost is denied to prevent the attack. Initially confusing to implementers, this asymmetric approach ensures that if a builder reveals based on seeing a timely block with sufficient support, any late equivocating block cannot gain proposer boost advantage. The mechanism protects builders by ensuring that once they reveal based on legitimate early support, malicious late equivocations cannot successfully reorganize their blocks.

Implementation details were clarified around signature verification requirements and p2p message handling. Action items include adding comprehensive tests for both equivocation scenarios and improving code comments on the `should_apply_proposer_boost` function, as the logic proved initially confusing to implementers who incorrectly implemented symmetric equivocation checking.

## Participants

- fradamt
- m.kalinin
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
