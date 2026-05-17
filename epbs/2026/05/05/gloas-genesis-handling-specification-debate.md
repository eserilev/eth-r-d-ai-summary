# Gloas Genesis Implementation Issues and Client Compatibility

**Channel:** epbs | **Date:** 2026-05-05

## Summary

The discussion centers around implementation inconsistencies in Gloas genesis block generation, specifically regarding how the genesis block should include execution payload bids. Sproul identified that the ethereum-genesis-generator used by Kurtosis wasn't following the spec's genesis definition, which requires putting the bid in the genesis block itself, not just the state. A proposed fix (PR #77) works correctly with Lighthouse and Nimbus but fails with Prysm, Lodestar, Teku, and Grandine, causing block root mismatches during initialization.

The root issue appears to be that different clients implemented various "hacks" during previous interop testing to work around the inconsistent genesis generation, and some may have permanently enshrined these workarounds. The failing clients show errors where the genesis block root doesn't match the genesis state's latest block header, suggesting they expect different genesis block construction. Participants confirmed that clients are passing consensus spec tests, indicating the spec itself is correct, but the genesis generator implementation diverges from the specification.

The consensus is that the specification is correct and PR #77 should align the genesis generator with the spec, but further investigation is needed to understand why certain clients reject the spec-compliant genesis blocks. The discussion suggests some clients may need to remove their genesis-specific hacks to properly support the standardized genesis format.

## Participants

- barnabasbusa
- etan_status
- nc1234
- nflaig
- pk910
- potuz
- sproul
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-05T00:42:02.088000+00:00] sproul: Sorry to be the bearer of bad news but Gloas genesis is still wonky 😅 
https://github.com/ethpandaops/eth-beacon-genesis/issues/76

Did other clients hack around this for interop too? IMO we should just use one consistent definition. CC <@808969530608451584> <@412614104222531604> <@586161934425128960> <@755590043632140352>
[2026-05-05T00:42:49.375000+00:00] sproul: cc <@881905303011086387>
[2026-05-05T05:03:44.840000+00:00] etan_status: yeah, genesis was quite tricky to get working with the Gloas light client tests. the latest one that sets parent_hash correctly in the genesis block works fine
[2026-05-05T05:07:47.455000+00:00] sproul: nice. Yeah I think conceptually and practically the spec’s genesis definition is good now.

had you observed the brokenness in the ethereum-genesis-generator used by kurtosis?
[2026-05-05T05:17:16.881000+00:00] etan_status: no, I did not run kurtosis for a while, but also note that the genesis stuff didn't change in my PR (https://github.com/ethereum/consensus-specs/pull/5173), I only exposed an additional helper for reuse in my own tests, no semantic change outside that
[2026-05-05T05:23:49.375000+00:00] sproul: oh yeah nice, it was a handy link for my bug report because it quite clearly shows the right definition not buried in heaps of other changes
[2026-05-05T05:24:45.017000+00:00] sproul: I didn't mean to imply "you changed the spec", it was already the case that Gloas genesis was only defined in test generators
[2026-05-05T05:25:27.313000+00:00] etan_status: yeah, I put it there because I couldn't find a spec, and the neighbour function is where I copied from
[2026-05-05T06:36:03.106000+00:00] barnabasbusa: i just did what potuz told me to do lol
[2026-05-05T09:14:25.248000+00:00] sproul: Potuzzzzzz!
[2026-05-05T09:14:56.847000+00:00] sproul: nah jk, I think nobody realised it was like this until the spec tests went out
[2026-05-05T10:46:02.928000+00:00] potuz: Wait what did I do wrong? Barnabas was giving me a clearly impossible state for genesis if it wanted to be post state of an empty slot as the spec required.
[2026-05-05T10:48:36.755000+00:00] potuz: So I just proposed the first solution here https://github.com/ethereum/consensus-specs/issues/5168
[2026-05-05T10:48:44.930000+00:00] potuz: Was that wrong?
[2026-05-05T11:30:19.093000+00:00] barnabasbusa: lodestar doesn't like your [pr](https://github.com/ethpandaops/eth-beacon-genesis/pull/77): 
```
May-05 11:28:59.541[]                 info: Initializing beacon from a valid checkpoint state slot=0, epoch=0, stateRoot=0x1f61a289a92af910c4054649680c27e7530ee2cfafb5a049bfe2e90b4fbfb228, isWithinWeakSubjectivityPeriod=true
 ✖ Error: Genesis block root 0xc6b18cd1592c537a54a4fc9ee3d80457d8dcfb149a1d24a3dbb6035d91b4e77b does not match genesis state latest block root 0x5fd9bcb81d982bfa6e786f6877ce7052055c44d3893a3273ac7065589ec8ce71
    at persistAnchorState (file:///usr/app/packages/beacon-node/src/chain/initState.ts:35:13)
    at checkAndPersistAnchorState (file:///usr/app/packages/beacon-node/src/chain/initState.ts:123:11)
    at initBeaconState (file:///usr/app/packages/cli/src/cmds/beacon/initBeaconState.ts:324:11)
    at Object.beaconHandler [as handler] (file:///usr/app/packages/cli/src/cmds/beacon/handler.ts:79:9)
```
[2026-05-05T11:30:44.402000+00:00] barnabasbusa: neither does teku:
```
Teku failed to start: java.lang.IllegalArgumentException: Genesis block root 0xe8e9b3a577792dbfa841973187c2f3c93330784a5282be9bfb5d5e6909cd15d2 does not match genesis state latest block root 0x8c413c5e50831ed24aebf79ccb573538b72e5c3c7562c5f49febceb48805c3c6

```
[2026-05-05T11:31:09.813000+00:00] barnabasbusa: neither does grandine
```
[2026-05-05T11:30:57.105Z]  INFO fork_choice_control::storage:225: loaded state at slot 0 peers=[0/200]

thread 'main' (7) panicked at fork_choice_store/src/store.rs:308:9:
assertion `left == right` failed
```
[2026-05-05T11:35:48.505000+00:00] barnabasbusa: I'm happy to do whatever we agree on. But we might need a new spec release?
[2026-05-05T11:37:22.474000+00:00] nc1234: I don't think we check the bid in genesis block body at all. We just take whatever `latestExecutionPayloadBid` in genesis state. Theoretically PR#77 should be okay with Lodestar. Looks like something is wrong with setting genesis state's `latestBlockHeader`
[2026-05-05T11:40:21.838000+00:00] sproul: no, your solution is great, the spec has an additional thing where it puts the bid in the genesis _block_
[2026-05-05T11:40:34.072000+00:00] sproul: which the genesis generator currently does not do
[2026-05-05T11:41:06.888000+00:00] sproul: ok this is weird, I don’t understand how they can dislike my change but pass the spec tests…
[2026-05-05T11:42:36.841000+00:00] sproul: possibly my PR is missing something, let me double check it
[2026-05-05T11:43:20.019000+00:00] potuz: oh this is on tests mostly, hmmm, right.
[2026-05-05T11:47:15.296000+00:00] sproul: how do you derive the genesis block from the genesis state, and do you pass the spec tests?
[2026-05-05T11:58:27.390000+00:00] barnabasbusa: <@602753420033785856> would you have a PR to fix the cl specs?
[2026-05-05T12:09:14.360000+00:00] sproul: specs are fine, it just needs to add some text saying what the current behaviour is
[2026-05-05T12:37:05.442000+00:00] barnabasbusa: so lighthouse/nimbus  seems to be happy with pr77, 
prysm/lodestar/teku/grandine seems to be unhappy with pr77
[2026-05-05T12:38:15.890000+00:00] barnabasbusa: prysm keeps complaining about: 
```
[2026-05-05 12:37:43.02] ERROR rpc/validator: Finished building block error=rpc error: code = Internal desc = Could not compute state root: could not compute state root: could not process block: could not process block header: parent root 0xdacaafce482f1b9511d4e474e1f9ee5d14888a13fe451050d6fffaf91ce236df does not match the latest block header signing root in state 0xc10066871e5cd89fe1c668ff3b37d6a6948135ac94b78f5d6234d39ffadac164 sinceSlotStartTime=27.01378ms slot=6 validator=107
```
[2026-05-05T12:39:17.800000+00:00] sproul: <@412614104222531604> do you know if any of Prysm/Lodestar/Teku/Grandine pass the spec tests? PR77 should just be the same as the spec tests
[2026-05-05T12:39:35.709000+00:00] barnabasbusa: they should be yeah
[2026-05-05T12:39:37.596000+00:00] sproul: at interop Lighthouse was using some hacks to be compatible with the current genesis gen
[2026-05-05T12:39:49.609000+00:00] barnabasbusa: we have been using fulu genesis on devnets
[2026-05-05T12:39:52.781000+00:00] sproul: maybe those clients have enshrined those hacks
[2026-05-05T12:40:04.185000+00:00] barnabasbusa: yeah thats what I'm afraid of too lol
[2026-05-05T12:40:04.529000+00:00] sproul: oh ok
[2026-05-05T12:41:09.842000+00:00] sproul: too late for me now but I am quite sure my change is right

I’ll go on the warpath with Claude tomorrow if it’s not fixed by then
[2026-05-05T12:50:49.684000+00:00] barnabasbusa: Current master:
```json
  "latest_block_header": {
    "slot": "0",
    "proposer_index": "0",
    "parent_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "state_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "body_root": "0x8c413c5e50831ed24aebf79ccb573538b72e5c3c7562c5f49febceb48805c3c6"
  },
  "latest_block_hash": "0x6bfa5317d970468c91f180fbc34bf943dc52566c6495fb6baddf244cab857d15",
  "latest_execution_payload_bid": {
    "parent_block_hash": "0x6bfa5317d970468c91f180fbc34bf943dc52566c6495fb6baddf244cab857d15",
    "parent_block_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "block_hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "prev_randao": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "fee_recipient": "0x0000000000000000000000000000000000000000",
    "gas_limit": "0",
    "builder_index": "0",
    "slot": "0",
    "value": "0",
    "execution_payment": "0",
    "blob_kzg_commitments": [],
    "execution_requests_root": "0x85e253b40599d0df756be043ea6949e49a07e756deef72b3588a4b05362206b5"
  },
```
[2026-05-05T12:50:53.696000+00:00] barnabasbusa: PR77:
```json
  "latest_block_header": {
    "slot": "0",
    "proposer_index": "0",
    "parent_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "state_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "body_root": "0x180484ddb1225a6f29aa5bfd008cd15f93c9417b109606c6c542c119b5b0352a"
  },
  "latest_block_hash": "0x9e0773e4a195fc4ce94bd9d51a6a734bd7942900936f62fdedfd16af4075e428",
  "latest_execution_payload_bid": {
    "parent_block_hash": "0x9e0773e4a195fc4ce94bd9d51a6a734bd7942900936f62fdedfd16af4075e428",
    "parent_block_root": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "block_hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "prev_randao": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "fee_recipient": "0x0000000000000000000000000000000000000000",
    "gas_limit": "0",
    "builder_index": "0",
    "slot": "0",
    "value": "0",
    "execution_payment": "0",
    "blob_kzg_commitments": [],
    "execution_requests_root": "0x85e253b40599d0df756be043ea6949e49a07e756deef72b3588a4b05362206b5"
  },
```
[2026-05-05T12:59:04.206000+00:00] barnabasbusa: these random hashes are probably not useful to anyone soz
[2026-05-05T12:59:05.493000+00:00] barnabasbusa: lol
[2026-05-05T13:33:11.291000+00:00] pk910: <@412614104222531604>  
that's exactly what I tried to impllement during the interop too.. We've reverted to the empty bid because clients didn't like it
[2026-05-05T13:40:53.050000+00:00] sproul: lighthouse only liked it because of the hacks, I’m going to go looking at the other clients tomorrow unless someone beats me to it
[2026-05-05T13:41:03.921000+00:00] sproul: the spec definitely puts the bid in the block
[2026-05-05T13:50:53.741000+00:00] potuz: cc <@363800010518822915> that implemented this, but I believe Prysm was passing spectests.
[2026-05-05T13:51:21.822000+00:00] potuz: Hey <@1297482247695499278> can I get those rewards? I
[2026-05-05T13:51:28.942000+00:00] potuz: ahh crap someone is faster than me typing
[2026-05-05T13:51:41.962000+00:00] barnabasbusa: too slow m8
[2026-05-05T14:15:21.341000+00:00] nflaig: hmm I removed all our genesis hacks and we are passing spec tests too, I don't believe we even use the genesis block at all other than checking the block root against the genesis state latest block root
[2026-05-05T15:41:53.244000+00:00] terencechain: consensus spec tests? we are passing them after removing the these hack:
```
    is_genesis_block = state.latest_block_hash == Hash32()
    is_parent_block_empty = state.latest_block_hash != state.latest_execution_payload_bid.block_hash
    if is_genesis_block or is_parent_block_empty:
```
```

</details>
