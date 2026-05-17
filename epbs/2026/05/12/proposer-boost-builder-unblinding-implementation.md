# Proposer boost and builder unblinding implementation challenges

**Channel:** epbs | **Date:** 2026-05-12

## Summary

Potuz is seeking advice on implementing proposer boost alongside Francesco's builder unblinding feature, specifically regarding how to handle the Phase 0 gossip validation rule that ignores blocks after the first valid block from a proposer in a slot. The main concern is that tracking equivocations over gossip would require validating signatures on potentially invalid blocks that arrive after the first block, as well as performing hash tree root (HTR) validation and difference checking on repeated messages, though libp2p already filters messages with identical hashes.

Potuz acknowledges that most implementation challenges may already be addressed by existing libp2p message filtering, but notes that HTR validation and difference detection would still be required. They're considering a naive approach of only validating equivocations during an early time window (for the current slot) and caching a boolean flag when catching valid signature equivocations, but are looking for guidance on whether there's a better implementation strategy.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
