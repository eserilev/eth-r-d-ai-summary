# Engine API FCU with empty blocks reorg issue

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers on a newly discovered issue with the Engine API's forkchoice updated (FCU) mechanism when handling reorganizations involving empty blocks. The problem occurs when the consensus layer reorgs to an empty block, requiring it to pass the parent block hash (rather than the tip) to the execution layer for the next block proposal. This violates current assumptions where FCU typically receives tip hashes that have no children.

Potuz illustrates the issue with a scenario where a reorg from block 36 to an empty block 34 appears to the execution layer as a reorg to block 33 (the parent), which is not a chain tip. This situation is occurring on devnet due to frequent empty block voting. The team identifies that the Engine API specification needs updating, specifically changing the MAY skip requirement to MUST support when `forkchoiceState.headBlockHash` references a valid ancestor of the canonical chain head.

The group agrees this functionality needs to be implemented while it's still easy to test on devnet, as the issue will become harder to reproduce once clients begin packing attestations with index=1. Potuz notes this change could also simplify the "honest reorg" feature implementation.

## Participants

- mariusvanderwijden
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T16:22:17.131000+00:00] potuz: <@360491619402776577> <@520034910149410861>, <@363800010518822915> noticed the following on devnet which is screwing us by having full forkchoice, but it's actually something that will need support from the EL that we hadn't noticed before. 

Today when there is a reorg, we send FCU with payload attributes to propose the next block, we are guaranteed to pass a tip of forkchoice to the EL, that is,the parent block hash we will pass has no children, and they start producing a block on top of that.  But on gloas, it can happen that you reorg into the *empty* block, thus you *may* pass the parent hash of a tip of that chain. 

So it can happen that you have 

```
(33, full) ----------------(35, full) <----- (36,full)      <--- head
      \-------(34, full)
       \______(34, empty)
```
And suddenly you find your self with more votes for (34,empty) causing you to reorg from 36, to 34, but pasing not the last hash (that hash of 34) but the hash at 33, which is never a tip of the chain. 

The EL must be able to produce these payloads. How do we solve this situation?
[2026-03-05T16:25:05.267000+00:00] mariusvanderwijden: Shouldn't the EL be capable of doing this? I thought that we allow building on parent blocks, we just don't allow FCU on a parent block
[2026-03-05T16:25:23.491000+00:00] potuz: well this is FCU on top of a parent block on the nose
[2026-03-05T16:25:50.022000+00:00] potuz: the reorg (36, full) ---> (34, empty) looks like a reorg 36 --> 33 from the point of view of the EL
[2026-03-05T16:26:04.759000+00:00] potuz: we are seeing this live because clients are voting on empty constantly in devnet
[2026-03-05T16:26:50.240000+00:00] terencechain: cc <@425572898787426305> , we also need an engine api update, in particular this line: https://github.com/ethereum/execution-apis/blob/main/src/engine/paris.md#engine_forkchoiceupdatedv1

`Client software MAY skip an update of the forkchoice state and MUST NOT begin a payload build process if forkchoiceState.headBlockHash references a VALID ancestor of the head of canonical chain`   this line becomes a MUST support
[2026-03-05T16:27:44.472000+00:00] potuz: This will also make the honest reorg feature easier probably 🙂
[2026-03-05T16:38:32.673000+00:00] potuz: if this could not be too difficult to add, it would be great to have it now that it's easy to test. As soon as clients start packing attestations with index=1 it'll be a little more awkward to trigger these reorgs
```

</details>
