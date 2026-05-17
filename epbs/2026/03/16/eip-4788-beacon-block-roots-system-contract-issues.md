# EIP-4788 Beacon Block Roots System Contract Issues

**Channel:** epbs | **Date:** 2026-03-16

## Summary

The discussion centers on a compatibility issue with EIP-4788's beacon block roots system contract. pk910 identified that when execution blocks are missed or reorganized, the beacon block root of the parent block never gets added to the contract's history, potentially breaking contracts that need to prove events from parent blocks like slashings or deposits for decentralized staking pools.

This behavior actually benefits EIP-8184 (LUCID encrypted mempool) as noted by anderselowsson, since it allows for a recovery mechanism where beacon block roots are added only after decrypted transactions execute. Two potential solutions were discussed: executing missed beacon root updates sequentially in separate execution contexts during the first full block after empty blocks, or leaving the current behavior unchanged. The latter approach would require changes to the Engine API to pass lists of empty block times and roots.

After further discussion, potuz argued that the current behavior is likely acceptable since beacon block roots are still available on-chain through the consensus layer state, though proving against specific blocks becomes more complex as it requires merkle proofs against the latest available root rather than direct EVM access. The group reached a tentative consensus to maintain the current behavior for simplicity, with plans to consult probable consumers of the system contract before making any final decisions.

## Participants

- anderselowsson
- lightclient
- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T04:10:50.878000+00:00] pk910: I'm not sure how to check general contract compatibility, but I've played with spamoor to create a transaction per block that just logs out the last 10 blockhashes & beacon roots from both system contracts.  That should be enough to catch differences.. But as both contracts update the state, I guess any mismatch would cause a fork due to mismatching state roots anyway.
[2026-03-16T04:12:48.387000+00:00] pk910: While playing around with the system contracts, I noticed another problem:
When execution blocks are missed or reorged out, the beacon block root of the parent block never gets added to the EIP-4788 (beacon block roots) contract. There is no system call that adds parents of empty blocks to the history. 
That's potentially breaking contracts that try to prove stuff that happened in the parent block (like slashings or deposits for decentralized staking pools)
I think we need to find a fix for this, so even parents of blocks with empty payloads make it into the EIP-4788 system contract history.
[2026-03-16T10:06:37.193000+00:00] potuz: I suspect the lucid guys like this feature from this behavior and that's why I ended up asking here. But it's good to raise it, probably we should at least mention this in ACD
[2026-03-16T18:08:35.331000+00:00] anderselowsson: Yes, the behavior that <@808969530608451584> describes interacts very well with EIP-8184, LUCID encrypted mempool. The reason is that this allows for the recovery mechanism that LUCID relies on to ensure that if you observe conditions allowing you to release a key, we guarantee that the transactions becomes canonical. Specifically, we wish to wait with adding the beacon block root until after the decrypted transactions have executed. It would thus be ideal if you could add all the missing beacon block roots at the same time when executing the first full payload, as this would simplify things greatly from a LUCID perspective.
[2026-03-16T18:12:24.367000+00:00] pk910: Adding multiple entries to the ring buffer is unfortunately not possible with the current system contract architecture :/
So we either have to do dirty storage hacks to bypass the on chain insertion logic, or deploy a new beacon roots system contract.
[2026-03-16T18:15:45.378000+00:00] pk910: ^cc <@543900561460822016>  (probably has more insights into the system contracts)
[2026-03-16T18:15:50.212000+00:00] potuz: cc <@543900561460822016>
[2026-03-16T18:15:54.074000+00:00] potuz: ah lol jinx
[2026-03-16T18:54:09.930000+00:00] lightclient: wdym by adding multiple entries to the ring buffer? if you want to only publish the beacon block hash / execution block hash after decryption, it seems like they can be done sequentially. you just have to work backwards to figure out what time stamp the evm was supposed to be at that time and submit it with that evm context
[2026-03-16T18:54:32.903000+00:00] lightclient: but that might have other downstream implications on consumers who expect the hashes available at n+1
[2026-03-16T21:44:02.580000+00:00] pk910: Ah yea, that's another approach, just executing the missed beacon root updates in their own execution context "in between" blocks..
I wonder if that has any implications with the zk world, as these updates are not part of the block execution context itself?
[2026-03-16T21:47:37.254000+00:00] anderselowsson: What does "in between" mean? I ask because for LUCID it would be best to execute all missed beacon root updates sequentially during processing of the first full block after a string of empty ones, so wanna check if that is the idea there..
[2026-03-16T21:53:36.797000+00:00] pk910: yea, I think that matches the idea.

assume this:
```
slots:   B0 B1 B2 B3 B4
payload: E0 E1 -- -- E4
```
block B2 & B3 are empty. 
For B4 the el clients have to execute the beacon root updates for E2 + E3 in their own execution context (with the block time of B2 & B3), then execute E4. all in one go, if the block is invalid, revert all. 

We'll need a change in the engine api to pass a list of empty block times + roots
[2026-03-16T21:58:32.705000+00:00] pk910: And we should probably delay this change till we have a merged glamsterdam network. Feels like a EL heavy change, that'll cause conflicts when merging the bal branches
[2026-03-16T22:37:52.225000+00:00] potuz: It seems to me the current behavior is probably good. There's probably only very niche applications in having those blockroots in the evm (notice that just the child root is good enough to get the others in the CL and prove against them)
[2026-03-16T22:46:20.363000+00:00] anderselowsson: That's an interesting point. I'll let you guys work it out 🙂
[2026-03-16T22:53:31.272000+00:00] pk910: It'd definitely simplify things when leaving it as is.  But the behavior feels a bit inconsistent then :/
In the example above, we'd never have the block roots of B1 & B2 on chain. the lack of B1 feels problematic as it's a full block with all kind of operations.
sure, you can go back the path from B3 to B1 via the ParentBlockRoots in proofs. But that path could get quite long under bad network conditions and several empty blocks in a row?
[2026-03-16T23:00:15.421000+00:00] potuz: They are on-chain, they're not in that system contract but they are on-chain. The main purpose of that contract is IMO to be able to prove against the CL as soon as you can. You cannot prove against B1 and B2 at all while there aren't payloads being executed. And you can as soon as you get E4 including B3 because the state for B3 also has commitments to B1 and B2.
[2026-03-16T23:00:41.265000+00:00] potuz: So from the point of view of the user here, I very much doubt it makes a lot of difference.
[2026-03-16T23:02:24.864000+00:00] potuz: I can imagine that proving that something happened in a particular beacon block root becomes much more difficult though, cause instead of proving directly against one of the blockroots available directly in the EVM you need to provide a merkle proof against the latest one. But this only affects proofs not about a current status (like being slashed or this or that) but against having received this CL reward at this specific block for example
[2026-03-16T23:14:33.912000+00:00] pk910: Oh yea,  you're right.  The state has a list of all recent block roots anyway, so the proof path length keeps the same.
It's only getting a bit more complex due to proofing through the state,  but guess that's fine.
[2026-03-16T23:28:25.829000+00:00] potuz: We should still ping some probable consumers of this.. cc <@935504619554107392>
[2026-03-16T23:28:38.624000+00:00] potuz: Don't know anyone at RP here
```

</details>
