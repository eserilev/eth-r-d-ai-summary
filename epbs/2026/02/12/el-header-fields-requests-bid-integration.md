# Moving EL header fields and requests into bid

**Channel:** epbs | **Date:** 2026-02-12

## Summary

The discussion centers around a proposal by nero_eth to move EL (Execution Layer) header fields and requests into the bid, leaving only transactions and withdrawals in the envelope. However, fradamt explains this approach was previously considered and rejected because future mechanisms like slot auctions might only allow committing to builder identity, whereas the current design enables full commitment without equivocation.

Potuz identifies a key technical constraint: the envelope currently commits to the beacon block root, which cannot be moved into the bid because it's needed for gossip functionality. When nero_eth suggests replacing it with slot number, potuz clarifies this won't work since multiple different blocks can exist in a single slot. Additionally, potuz notes that the state root depends on the post-state of the beacon block, meaning builders cannot seal the envelope until after seeing the block.

The consensus appears to be that while other elements could potentially be moved to the bid (with an extra root added), the beacon block root must remain in the envelope for gossip purposes. Potuz suggests this restructuring would be "overkill and unnecessary" since the changes would cost money ex-ante without providing useful benefits for payload reordering.

## Participants

- fradamt
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-12T10:18:25.416000+00:00] nero_eth: you could move everything else (EL header fields + requests) into the bid, I guess? Then the envelope would only contain transactions and withdrawals.
[2026-02-12T11:58:00.146000+00:00] fradamt: We talked about this before, and it was a choice not to do this because in the future there might be other mechanisms (slot auctions) such that it is actually impossible to commit to anything other than a builder identity. However, in the current design it is possible to have a full commitment such that there cannot be any equivocation.
[2026-02-12T12:59:19.494000+00:00] potuz: we've already talked about this when you requested to remove the state root, with the exact current design it's impossible
[2026-02-12T13:01:16.110000+00:00] potuz: I'm just arriving from a flight, but will find the detailed discussion when we had this already. I even went to write the PR to remove the state root and realized it was not that useful cause of this
[2026-02-12T13:11:52.227000+00:00] potuz: I can't find the discussion, but today the envelope commits to the beacon block root, which can't be committed therefore in the bid
[2026-02-12T13:13:26.222000+00:00] potuz: we can commit everything else I believe (except the state root that will have to be removed I think) and add an extra root to the bid. But I suspect this is overkill and unnecessary since ex-ante cost money and are not useful since you aren't reorging the next payload
[2026-02-12T13:14:08.823000+00:00] nero_eth: why do we need the envelope to commit to the beacon block root?
[2026-02-12T13:14:21.608000+00:00] potuz: we need it for gossip
[2026-02-12T13:15:16.337000+00:00] nero_eth: I see, could we replace it with the slot num?
[2026-02-12T13:15:28.502000+00:00] potuz: no, there could be many different blocks in a slot
[2026-02-12T13:16:00.782000+00:00] nero_eth: hmm, right.
[2026-02-12T13:16:41.209000+00:00] nero_eth: and why can't we commit to the state root?
[2026-02-12T13:17:00.003000+00:00] potuz: Was boarding at the time you sent this, it's the beacon block root I mention above.
[2026-02-12T13:17:47.614000+00:00] potuz: I believe the state is dependent on the post-state of the beacon block, the builder can't seal the envelope until after it's seen the block
[2026-02-12T13:18:39.567000+00:00] potuz: That one can in principle be removed without consequences from the envelope, but the block root I think it would be a little hurtful
```

</details>
