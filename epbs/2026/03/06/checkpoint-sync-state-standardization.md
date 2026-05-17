# Checkpoint sync implementation and API discussion

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers on implementing checkpoint sync for an ePBS (enshrined proposer-builder separation) devnet, with participants working through API compatibility issues between different Ethereum clients. The main technical challenge involves determining what state should be served by the checkpoint sync API - there's an ongoing debate (referenced in GitHub issue #572) about whether to serve pre-block or post-block state, with the consensus emerging that the server should provide a beacon block B and beacon state S where `S.LatestBlockHash == B.SignedExecutionPayloadBid.Message.ParentBlockHash`.

The team identified that different clients are serving incompatible data: Lighthouse serves the execution layer post-state (which is incorrect), Lodestar has issues serving finalized state entirely, while Prysm serves the expected pre-payload state object. As a result, they configured the checkpoint sync service to only use Prysm for now. Potuz successfully tested checkpoint sync with this Prysm-only configuration and reported it working, though noted some Geth optimization issues likely related to database gaps.

The key remaining challenges include getting Lighthouse and Lodestar to serve compatible states so the checkpoint service isn't dependent on a single client implementation. Nflaig reported progress on Lodestar's range sync and envelope serving capabilities, with some previous bugs fixed, though spec-related issues persist. The immediate action item is for Lodestar developers to fix their finalized state serving issue so they can be re-added to the checkpoint service.

## Participants

- barnabasbusa
- inspector.butters
- nflaig
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T10:01:50.437000+00:00] barnabasbusa: <@641548687813902336> got an update for me?
[2026-03-06T10:11:29.009000+00:00] barnabasbusa: <@358120958726373381> <@586161934425128960> should sync now work on lh / lodestar?
[2026-03-06T10:11:44.898000+00:00] nflaig: not with the current branch
[2026-03-06T10:11:56.333000+00:00] barnabasbusa: got a new branch for me to test?
[2026-03-06T10:12:38.619000+00:00] nflaig: I tried yesterday or rather tonight and didn't manage to go through epoch 142
[2026-03-06T10:14:15.307000+00:00] barnabasbusa: https://checkpoint-sync.epbs-devnet-0.ethpandaops.io
[2026-03-06T10:14:19.956000+00:00] barnabasbusa: we can now try this ?
[2026-03-06T10:14:26.024000+00:00] barnabasbusa: it should be updated to the latest spec
[2026-03-06T10:14:36.515000+00:00] barnabasbusa: so should be able to serve what clients need
[2026-03-06T10:14:57.685000+00:00] nflaig: which spec do you mean?
[2026-03-06T10:15:32.242000+00:00] barnabasbusa: epbs
[2026-03-06T10:15:37.083000+00:00] nflaig: https://github.com/ethereum/beacon-APIs/issues/572 is still being discussed, I am not sure we can checkpoint sync
[2026-03-06T10:15:55.744000+00:00] barnabasbusa: ah right
[2026-03-06T10:16:01.279000+00:00] nflaig: so you serve post block state?
[2026-03-06T10:16:04.297000+00:00] barnabasbusa: what's the plan with this?
[2026-03-06T10:16:28.482000+00:00] barnabasbusa: <@84562395988508672> what did you use as reference?
[2026-03-06T10:16:30.863000+00:00] nflaig: I think potuz last comment makes sense, but need more feedback from others
[2026-03-06T10:40:42.022000+00:00] stefanbratanov: I am trying to get checkpoint sync working, not sure about that fork choice error, wasn't able to fix that one
[2026-03-06T10:41:23.743000+00:00] stefanbratanov: that's serving the block state?
[2026-03-06T10:56:59.181000+00:00] barnabasbusa: it servers block and state on `/eth/v2/debug/beacon/states/finalized` and `/eth/v2/beacon/blocks/finalized`
[2026-03-06T10:57:42.188000+00:00] potuz: Oh I came here to ask about checkpoint sync, I'll try to work on this today. <@412614104222531604> sorry for the newbie questions, I do not know anything about checkpoint sync. I was undert ehe idea that the server serves state and block and that the node checks that the state is the post-state of applying that block, is that the case?
[2026-03-06T10:58:57.344000+00:00] potuz: Namely the server should be serving a beacon block  `B` and a beacon state `S` such that the `latest_block_hash` in `S` is the `parent_block_hash` in the bid of `B`. Is this correct?
[2026-03-06T10:59:25.768000+00:00] potuz: we just request then payloads and blocks starting from the same slot of the state (as opposed to the next one as we do now) and import the payload if needed
[2026-03-06T11:00:26.005000+00:00] potuz: So questions for you <@412614104222531604>, which CL client are you using to provide that state/block? and 2) have you vetted that the server provides the data as I mention above?
[2026-03-06T11:00:44.417000+00:00] barnabasbusa: currently checkpointz is hooked up to lighthouse/lodestar/prysm
[2026-03-06T11:01:01.563000+00:00] barnabasbusa: I'm looking into this specific question regarding the bid stuff
[2026-03-06T11:03:02.285000+00:00] potuz: Those three clients may give different replies, as far as I know the API owners were still arguing what state should be returned. I just hope it converges to the above
[2026-03-06T11:04:15.907000+00:00] potuz: But anyway if you point to Prysm, unless <@1057691141237125190> lied to me, it should give me the pair state/block as I hoped (Is this true <@1057691141237125190> ?)
[2026-03-06T11:04:51.147000+00:00] barnabasbusa: State S does not include the execution payload for this slot (it's separate from the block) → you need to also fetch and import the execution payload envelope for the same slot, then continue from there
[2026-03-06T11:05:16.405000+00:00] barnabasbusa: you request payloads and blocks starting from the same slot as the state, import the payload if it was revealed, and then proceed forward. The current "start from next slot" logic would miss the execution payload at the finalized slot.
[2026-03-06T11:05:59.672000+00:00] barnabasbusa: S.LatestBlockHash == B.SignedExecutionPayloadBid.Message.ParentBlockHash
[2026-03-06T11:07:19.480000+00:00] potuz: perfect, that's exactly what I want
[2026-03-06T11:07:48.466000+00:00] inspector.butters: my gaming tag is Ugly But Honest.

right now the endpoint eth/v2/beacon/debug/states/{id} works on prysm for every id except state root. that will also work when https://github.com/OffchainLabs/prysm/pull/16466 merges. 
but this gives you only the pre-payload state object
[2026-03-06T11:08:17.361000+00:00] barnabasbusa: lodestar seems to be struggling to serv eteh finalized state on beacon api
[2026-03-06T11:08:35.763000+00:00] potuz: Nico I think that epoch has an empty block! that actually broke Prysm sync too when <@412614104222531604> restarted the pods the other day
[2026-03-06T11:08:42.339000+00:00] barnabasbusa: 
[2026-03-06T11:10:18.870000+00:00] potuz: ok, take only Prysm then
[2026-03-06T11:10:29.980000+00:00] potuz: Lighthouse is serving the EL post-state
[2026-03-06T11:10:35.489000+00:00] potuz: and Lodestar I have no clue
[2026-03-06T11:10:36.282000+00:00] barnabasbusa: Yeah seems to be a bug in LH
[2026-03-06T11:10:47.801000+00:00] barnabasbusa: and lodestar is just borked
[2026-03-06T11:10:55.416000+00:00] barnabasbusa: I can remove lh and lodestar for now from checkpointz
[2026-03-06T11:11:12.963000+00:00] potuz: If you serve from Prysm pretty please I can try in a couple of hours, need to see my kids waking up
[2026-03-06T11:13:20.525000+00:00] barnabasbusa: do we want validatation for this in checkpoitnz ?
[2026-03-06T11:13:31.298000+00:00] barnabasbusa: or just trust that the clients will serve the right thing?
[2026-03-06T11:54:06.716000+00:00] potuz: I don't know anything about this, you can do the same kind of validation you currently do, there will be analogs for Gloas, if you tell me what you validate I can tell you for Gloas. I imagine that you check that the block is compatible with the state somehow? for example that the latest header in the state corresponds to the data in the block. Something similar can be done for Gloas, the latest header check remains the same, but also the latest bid has to be the latest bid in the block, and as you noticed above the latest block hash has to be the same as the parent block hash in the bid
[2026-03-06T12:35:43.791000+00:00] potuz: did you update the node to serve only from Prysm?
[2026-03-06T12:35:50.963000+00:00] barnabasbusa: ah not yet
[2026-03-06T12:35:55.705000+00:00] potuz: ahh shit
[2026-03-06T12:35:57.678000+00:00] barnabasbusa: Hold on will do this asap
[2026-03-06T12:35:59.187000+00:00] potuz: that's why
[2026-03-06T12:36:02.145000+00:00] potuz: lol
[2026-03-06T12:39:10.367000+00:00] barnabasbusa: try now plz
[2026-03-06T12:39:16.802000+00:00] barnabasbusa: sorry got distracted
[2026-03-06T12:40:07.895000+00:00] potuz: up to head in a beat
[2026-03-06T12:40:36.632000+00:00] potuz: working fine! thanks <@412614104222531604> will push to our sync branch, we should have a devnet zero branch merged with sync code soon to avoid having two branches
[2026-03-06T12:41:35.273000+00:00] potuz: geth is still optimistic for some reason though
[2026-03-06T12:41:40.489000+00:00] potuz: I will blame that on geth
[2026-03-06T12:43:06.845000+00:00] barnabasbusa: yeah
[2026-03-06T12:43:12.118000+00:00] barnabasbusa: that will take a while
[2026-03-06T12:43:18.249000+00:00] barnabasbusa: if you start fresh
[2026-03-06T12:43:27.324000+00:00] potuz: didn't start fresh
[2026-03-06T12:43:34.497000+00:00] potuz: kept geth's db from yesterday
[2026-03-06T12:43:58.006000+00:00] potuz: Getting these `ERROR[03-06|09:43:18.216] Error in block freeze operation          err="canonical hash missing, can't freeze block 2648"` I assume it's because of the gap
[2026-03-06T20:42:06.705000+00:00] potuz: It'd also be nice to have another checkpoint server other than prysm
[2026-03-06T20:42:31.545000+00:00] potuz: Otherwise if we die we won't be able to sync back up quickly
[2026-03-06T20:58:43.779000+00:00] barnabasbusa: we dont need another server, we need lodestar and lighthouse to serve the same state
[2026-03-06T20:59:04.953000+00:00] barnabasbusa: yes we do have some assertoor tests for all these
[2026-03-06T20:59:20.691000+00:00] nflaig: what's the tldr on this? who's wrong?
[2026-03-06T20:59:32.856000+00:00] barnabasbusa: lodestar isn't serving anything now
[2026-03-06T20:59:39.643000+00:00] barnabasbusa: <@586161934425128960>
[2026-03-06T21:00:06.408000+00:00] barnabasbusa: <@586161934425128960>
[2026-03-06T21:00:26.051000+00:00] barnabasbusa: if you have a fix for this let me know and I'll add lodestar back to the checkpointz server list
[2026-03-06T21:00:57.871000+00:00] nflaig: ok didn't follow that discussion, will see, on the bright side, we have range sync working and can serve envelopes by range
[2026-03-06T21:01:17.200000+00:00] nflaig: also ptc bug from way earlier fixed, although spec bug is still a thing of course
```

</details>
