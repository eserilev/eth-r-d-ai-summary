# ePBS block parent full/empty detection without fetching parent

**Channel:** epbs | **Date:** 2026-02-20

## Summary

In ePBS (enshrined Proposer-Builder Separation), dapplion raised a question about determining whether a parent block is full or empty without fetching the parent block itself. Currently, this requires reading `bid_parent_root` and `bid_parent_hash` from child block A, then fetching parent block B to get its `block_hash` from the execution payload bid. If the bid parent root matches B's root and the bid parent hash matches B's block hash, then A is payload-carrying (built on B's payload chain); otherwise A is empty.

The concern is that reverse lookup sync becomes sequential, requiring fetching block A, then block B, then B's payload. Dapplion suggested it might be useful for block A to explicitly express whether it built on top of an empty or full parent to avoid this dependency.

Potuz responded that the current approach of checking against the parent block root's bid is what they already use, and questioned the usefulness of expressing this information directly in the block. He noted that for syncing, the parent must already be in forkchoice with the payload available, and for backfill operations, the parent would need to be fetched anyway. No clear consensus was reached on whether this optimization would be beneficial.

## Participants

- dapplion
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-20T07:00:22.294000+00:00] dapplion: In ePBS given a SignedBeaconBlock can I know its parent is full/empty without having to fetch the parent? Now I need to do:

For child A and beacon parent B (B.root == A.parent_root):

  - Read from A:
      - bid_parent_root_A
      - bid_parent_hash_A
  - Read from B:
      - block_hash_B (from B’s signed_execution_payload_bid.message.block_hash)

  Then:

  - If bid_parent_root_A == B.root and bid_parent_hash_A == block_hash_B:
      - A is payload-carrying (built directly on B’s payload chain).
  - Otherwise:
      - A is empty (its payload parent skips over B).
[2026-02-20T07:02:44.888000+00:00] dapplion: In reverse lookup sync then I have to (sequentially)
- fetch block A
- fetch block B
- fetch payload of block B

Do you see useful to make block A express if it built on top of empty / full without having to fetch its parent?
[2026-02-20T07:51:03.178000+00:00] potuz: That's what we do, we check agains the bid of the parent block root. I am uncertain of the usefulness of expressing this in the block. For syncing, we must have the parent in forkchoice and this already has the payload there. I am unaware of any need to know if building on top of full or empty without fetching the parent. For example for backfill, you'll be fetching the parent anyway
```

</details>
