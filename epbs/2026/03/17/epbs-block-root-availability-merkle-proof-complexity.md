# EPBS Block Root Availability and Merkle Proof Complexity

**Channel:** epbs | **Date:** 2026-03-17

## Summary

The discussion centers on how EPBS (Enshrined Proposer-Builder Separation) will impact block root availability and Merkle proof complexity for applications that need to prove on-chain events like withdrawals. Under EPBS, not every block root will appear in the beacon roots contract (EIP-4788), specifically when parent roots of blocks with empty payloads are involved. This creates challenges for applications like dgusakov's that prove exact events in specific blocks.

The proposed workaround involves extending Merkle proofs to traverse from the last available block root through the state root to access historical block roots, then proceeding to the target operation. pk910 calculated this would require 36 sibling hashes (22 for beacon root traversal + 14 for withdrawal root) compared to the current 16 hashes, effectively doubling proof complexity and gas costs. However, if the beacon roots contract is "fixed" to include all necessary roots, proofs could actually become cheaper at 14 hashes.

The conversation also explored adding an `execution_payload_root` to bids to enable direct payload proofs, which potuz confirmed is technically feasible since it only requires the payload's hash tree root (unlike the full envelope which contains unknown fields at bid time). ralexstokes concluded there should be minimal impact since proofs can be extended by "a hash or two" to reach any required historical data, though the exact implementation details for optimal proof efficiency remain to be determined.

## Participants

- ajsutton
- dgusakov
- nixo.eth
- pk910
- potuz
- ralexstokes

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-17T04:39:45.655000+00:00] nixo.eth: ^ <@376562530463776787>
[2026-03-17T08:25:23.391000+00:00] dgusakov: Gm! It is a bit hard to figure out the exact changes we are talking about here. Can I ask for a sort of summary?
[2026-03-17T08:32:38.298000+00:00] dgusakov: We do prove exact events in exact blocks. Withdrawals, for example. If you are about to introduce the change where some blocks can be obtained directly while others via N jumps to parent, it will be problematic for us for sure
[2026-03-17T10:00:47.148000+00:00] pk910: that's basically exactly what we'Re talking about 😅 
with epbs we won't have every block root on chain anymore.  
there are situations where a block root of a regular block does not appear in the beacon roots contract.

the workaround we've discussed above is, that the proof could be extended to not start at the block root that includes the operation that should be proved. Instead, extend the proof to go from the last available block_root -> state_root -> block_roots[]   and from there to the individual operation (eg. withdrawals).

it'll make the proofs longer and more complex.
[2026-03-17T10:03:27.913000+00:00] pk910: proving withdrawals could actually be problematic with epbs 🤔 
we don't have a execution_payload_envelope_root or execution_payload_root in the bid,   so there's no merkle proof path towards the ExecutionPayload?
cc <@755590043632140352>
[2026-03-17T10:06:39.538000+00:00] ajsutton: oof, that sounds painful.  Is there a way to know if a block hash is or isn't going to be available easily? OP Stack uses the on-chain block hash history as part of proving derivation as it makes it _way_ faster to jump a long way back in history without having to walk through the block chain individually. We can be flexible about the process and already handle doing the lookup in multiple hops but would have to make adjustments if we can't look up an arbitrary block within the history range.
[2026-03-17T10:08:51.572000+00:00] pk910: parent roots of blocks with empty payloads won't be on chain.   but don't mix the block hash with beacon block roots.
we're talking about the EIP-4788 contract,  not EIP-2935 (block hashes)
[2026-03-17T10:11:08.722000+00:00] ajsutton: Ah, that's fine then - I only care about EL block hashes in this case.  OP Stack passes through the last beacon block header from L1 but we'll just continue to mirror whatever that is on L1 so should be fine.
[2026-03-17T10:17:46.624000+00:00] potuz: Withdrawals that were deducted in block N on the CL have to be paid in block N+k in the EL with some k>=0. The block root that will appear in the contract is N+k-1 but most importantly, they are cashed on the state until they are paid. If withdrawals are proved against inclusion in a Payload I believe it shouldn't be much different. If they are proved against deduction in the CL at a particular CL block yes this can be an issue, but if it's proven against the beacon state of that block (that cashed the withdrawals) then it should be fine as well
[2026-03-17T10:31:25.505000+00:00] pk910: does it make sense to add a execution_payload_root to the bid? that way apps can construct merkle proofs into the payload too.
could be interesting if we go forward with ssz and transactions & co become structured ssz objects too.  then everything within a block or its payload would be provable via the block root
[2026-03-17T10:42:41.748000+00:00] potuz: That can't be added
[2026-03-17T10:43:33.995000+00:00] potuz: Because there are fields in the envelope that can't be known at that time. Incidentally withdrawals is one of them
[2026-03-17T11:10:11.262000+00:00] potuz: But yes, life for these kinds of problems would be much easier if EL devs bit the bullet and moved to SSZ in the whole pipeline.
[2026-03-17T11:15:40.030000+00:00] pk910: I don't fully get that argument.   the block_hash is included in a bid, which is the hash over the whole execution block header, which includes the withdrawals_root.  So there is already a cryptographic commitment for the included withdrawals in the bid?
the execution_payload_root would just be another representation of the same data?

Anyway, I'll gonna dig into the proof complexity a bit.. let's see how big these proofs really get when going through the state / checking expected_withdrawals.
[2026-03-17T11:20:14.709000+00:00] potuz: The bid does not include the beacon block root of the current root, it includes the beacon block root of the parent beacon block. In the happy case the bid is for a payload that will fullfil withdrawals for the *current* beacon block which is not known yet at the time of bid submission
[2026-03-17T11:21:31.778000+00:00] potuz: The builder can always include a HTR for the full execution payload object within the envelope, but it cannot provide an HTR of the full envelope where some extra necessary data is included.
[2026-03-17T11:22:54.721000+00:00] pk910: that's what I'm talking about 😉  I've never mentioned a execution_payload_envelope_root
[2026-03-17T11:31:31.260000+00:00] potuz: ah yeah, for just the payload's HTR would work for this particular case, and yes, getting the payload HTR instead of the hash in the bid would be WAAAAY better, now go an advocate in ACDE for them to include this 😄
[2026-03-17T11:32:04.825000+00:00] potuz: BTW, we could do this independent of the EL devs if we agreed, the bid is on the CL only
[2026-03-17T11:58:15.494000+00:00] pk910: so, a proof from a beacon root  to a previous beacon root via block_roots needs 22 sibling hashes.
going from a block root to a specific withdrawal root needs 14 sibling hashes.
total 36 hashes per merkle proof verification for a withdrawal.

right now, proving a specific withdrawal directly from the right beacon root and traversing through body_root -> execution_payload -> withdrawals needs 16 sibling hashes.

so the complexity / proof size and gas costs would basically double when leaving it as is.
if we get the beacon roots contract fixed, we can get rid of the first 22 siblings,  making the proof via the state actually cheaper than before 😄
[2026-03-17T22:35:48.930000+00:00] ralexstokes: and also catching up on the "missing parent beacon block roots under epbs" issue above <@808969530608451584> <@755590043632140352> 

like potuz points out you can't really do anything without "the next execution block", so i dont see much value in modifying the engine API to capture them somehow (e.g. what EL timestamp should the empty beacon blocks associate with ?)

and also like potuz is saying you can prove anything from any given block root so if you need to peek back into some history that isn't immediately reachable via the 4788 contract, you'd just extend the proof by a hash or two

( so cc <@935504619554107392>  there shouldn't be any impact to what you are doing w/ withdrawals)
```

</details>
