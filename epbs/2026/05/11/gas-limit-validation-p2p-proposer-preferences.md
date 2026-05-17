# Gas limit validation in P2P and proposer preferences

**Channel:** epbs | **Date:** 2026-05-11

## Summary

The discussion centers on a problem with gas limit validation in the P2P gossip checks for Ethereum's ePBS (enshrined Proposer-Builder Separation) implementation. The current spec incorrectly checks for exact equality between the bid's gas limit and proposer preferences (`bid.gas_limit == proposer_preferences.gas_limit`), but this should instead validate against the parent block's gas limit using EIP-1559's transition rules that allow gradual changes up to 1/1024th of the parent's limit per block.

The conversation reveals confusion about whether the bid's gas limit field represents the proposer's target/desired gas limit or the actual calculated gas limit that should appear in the execution payload. This ambiguity creates inconsistencies in the spec where proposer preferences contain target values but gossip validation expects exact matches. The team discusses two potential approaches: having the bid contain the actual calculated gas limit (requiring CL to implement EIP-1559 validation) or having it contain the target limit and storing actual values in the beacon state. There's also discussion about consolidating gas limit control to the consensus layer rather than having both CL and EL flags, potentially through modifications to `engine_forkchoiceUpdatedV*`.

Potuz commits to opening PRs to address the immediate validation issue, with the simpler approach being to make bids contain actual gas limits and have the CL verify EIP-1559 compliance. The discussion acknowledges that most CL clients already implement this validation for builder payload verification. An additional complication noted is that the beacon state no longer stores actual gas usage, making CL-side validation more challenging, and there's mention of potential interactions with EIP-8073 though it appears not to affect the core gas limit transition rules.

## Participants

- barnabasbusa
- bharath.vedartham
- jtraglia
- nero_eth
- nflaig
- potuz
- tbenr
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-11T11:42:06.436000+00:00] bharath.vedartham: I think this p2p check for the gas_limit of the bid seems wrong: https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/p2p-interface.md?plain=1#L350 where we check for `bid.gas_limit == proposer_preferences.gas_limit`
We need to check the gas limit based on the parent block gas limit. This is how prysm does it today: https://github.com/OffchainLabs/prysm/blob/6a2dfaab953d1e87d51e39d03b1f7d3da550df96/beacon-chain/rpc/prysm/v1alpha1/validator/proposer_bellatrix.go#L446
In the event of a gas limit update, lets say from 60M to 200M we would require bids post the update to have 200M gas limit bids but that is not feasible for the EL since the gas limit is updated over blocks
[2026-05-11T12:13:23.184000+00:00] nero_eth: I was just chatting with <@1386598056320831509> about proposer preferences and gas limits.
One can use a check like the following to ensure the bid commits to the right gas limit:
```
max_delta = parent_gas_limit // 1024

new_gas_limit = max(
    parent_gas_limit - max_delta,
    min(
        preferred_gas_limit,
        parent_gas_limit + max_delta,
    ),
)
```

One thing I was wondering, how does the interaction with the EL work here?
E.g. I could have a CL flag increasing the gas limit (`--suggested-gas-limit`) and a EL flag that decreases it (`--miner.gaslimit`). Would my own bid be rejected by my CL or is there some exception for self-bidding?
[2026-05-11T12:14:20.163000+00:00] barnabasbusa: EL won't have much say in it anymore I think
[2026-05-11T12:15:49.743000+00:00] potuz: I think we seriously need to move to have the CL choosing the gas limit instead of the el. This is needed also to implement a builder client. Have been talking to Nico about this. And we all seem to agree. Need to convince EL devs though
[2026-05-11T12:16:55.639000+00:00] nflaig: EL devs are ignoring me https://discord.com/channels/595666850260713488/688075293562503241/1501977234078961774 😄
[2026-05-11T12:17:37.100000+00:00] potuz: Bring it up today in ACDT
[2026-05-11T12:19:43.273000+00:00] nero_eth: yeah agree. Should be consolidated to only have one and it makes sense to have it on the CL. Would this mean we need to add it to `engine_forkchoiceUpdatedV*` ?
[2026-05-11T12:20:35.588000+00:00] barnabasbusa: Could we also bundle in a max gas limit while we are at it ?
[2026-05-11T12:20:41.395000+00:00] barnabasbusa: so we can't accidently kill the network?
[2026-05-11T12:21:02.543000+00:00] nflaig: this is just the EIP-1559 caluclation? I suppose most (or all) CLs already have that implemented to ensure builders adhere to gas limit, eg. see [lodestar code](https://github.com/ChainSafe/lodestar/blob/unstable/packages/beacon-node/src/execution/builder/utils.ts)
[2026-05-11T12:21:37.392000+00:00] barnabasbusa: isn't gonna be a lot harder with 8073 tho?
[2026-05-11T12:21:45.184000+00:00] bharath.vedartham: yeah i think we would have to run that in the gossip checks instead of checking for `bid.gas_limit == pref.gas_limit`
[2026-05-11T12:23:40.601000+00:00] nflaig: why? I believe this can be handled on the EL side when we receive the payload, I don't think the CL needs to handle that calculation after gloas
[2026-05-11T12:24:05.794000+00:00] nero_eth: yeah, it's just that today, this isn't so critical as builders take care of it and relays make sure that they do things right. Now, with ePBS, the CL might want to actually check/enforce it. I think CLs do already, so the only open question is it's interaction with the gas limit set on the EL.

My feeling is we should deprecate the flag on the EL and have the gas limit be passed with `engine_forkchoiceUpdatedV*`, as done in the PR you linked in execution-devs
[2026-05-11T12:24:50.145000+00:00] bharath.vedartham: we could get into a situation where you accept a bid with an invalid gas limit right? we should reject a bid whose gas limit is invalid right?
[2026-05-11T12:25:06.351000+00:00] potuz: I would rather check the bid on gossip as we do today
[2026-05-11T12:25:39.376000+00:00] nflaig: the gas limit is not in the bid, it's in the execution payload header whcih you only know when receiving the payload
[2026-05-11T12:26:03.143000+00:00] tbenr: thanks i'll take a look!
[2026-05-11T12:26:31.130000+00:00] bharath.vedartham: we have the gas limit in the bid: https://github.com/ethereum/consensus-specs/blob/031e521f0a82b0e475ecfc13f79dd251a6284fc2/specs/gloas/beacon-chain.md#L283
[2026-05-11T12:26:43.176000+00:00] bharath.vedartham: ```python
class ExecutionPayloadBid(Container):
    parent_block_hash: Hash32
    parent_block_root: Root
    block_hash: Hash32
    prev_randao: Bytes32
    fee_recipient: ExecutionAddress
    gas_limit: uint64
    builder_index: BuilderIndex
    slot: Slot
    value: Gwei
    execution_payment: Gwei
    blob_kzg_commitments: List[KZGCommitment, MAX_BLOB_COMMITMENTS_PER_BLOCK]
    execution_requests_root: Root
```
[2026-05-11T12:26:55.754000+00:00] bharath.vedartham: so i think it is better to check in the gossip validation
[2026-05-11T12:27:01.294000+00:00] nflaig: I do know that but is that the calclulated or desired gas limit?
[2026-05-11T12:27:59.473000+00:00] bharath.vedartham: so the spec says that it must be the gas limit of the constructed payload: https://github.com/ethereum/consensus-specs/blob/031e521f0a82b0e475ecfc13f79dd251a6284fc2/specs/gloas/builder.md#L125
[2026-05-11T12:28:23.277000+00:00] bharath.vedartham: i believe its the calculated gas limit of the payload
[2026-05-11T12:29:26.678000+00:00] nflaig: but proposer preferences have the target gas limit
[2026-05-11T12:29:51.386000+00:00] nflaig: hmm so the bid check in spec seems wrong if that is the expected (actual) gas limit
[2026-05-11T12:30:13.809000+00:00] potuz: no, at least my reading was as yours Nico
[2026-05-11T12:30:25.136000+00:00] potuz: that the bid is not the expected actual gas limit, but what the guilder should signal
[2026-05-11T12:30:28.701000+00:00] jtraglia: That's the proposer's desired gas limit. Same as in mev-boost registrations today.
[2026-05-11T12:30:35.078000+00:00] potuz: that's the whole point of letting validators have gas limit
[2026-05-11T12:30:40.979000+00:00] potuz: exactly
[2026-05-11T12:30:47.202000+00:00] potuz: we should not check bid logic
[2026-05-11T12:30:55.498000+00:00] potuz: we should just let the CL drive the whole thing
[2026-05-11T12:31:21.968000+00:00] potuz: EL checks validity of 1559
[2026-05-11T12:31:58.774000+00:00] nflaig: that's what I thought, would allow us to remove the EL code from our code
[2026-05-11T12:32:29.751000+00:00] bharath.vedartham: we might run into a situation where we choose a bid with the wrong updated gas limit, and the corresponding payload is invalidated right?
[2026-05-11T12:33:07.099000+00:00] bharath.vedartham: if we don't check that the `bid.gas_limit` is correct from the parent gas limit
[2026-05-11T12:37:04.435000+00:00] barnabasbusa: would any clients oppose to set a max limit for the desried gas limit? e.g if we aim to hit 200m for glamsterdam but we do find some issues and we see that we could use 175M comfortably, then we should probably have an check where the user can override the default gas limit up till a max cap of 175 or whatever we deem safe? Similar idea to EIP-3756.
[2026-05-11T12:48:04.323000+00:00] nflaig: there is some inconsistency in the spec we need to fix
1. `proposer_preferences.gas_limit` is the desired/target gas limit (eg. 60M)
2. the bid needs to adhere to that `bid.gas_limit == proposer_preferences.gas_limit` (gossip)
3. the payload needs to adhere to the bid `assert payload.gas_limit == bid.gas_limit` (fc check)
4. `payload.gas_limit` is the actual/expected gas limit (https://discord.com/channels/595666850260713488/874767108809031740/1503369429239595168)
5. we don't pass `bid.gas_limit` to EL to validate this for us
[2026-05-11T12:54:11.220000+00:00] bharath.vedartham: If ‘payload.gas_limit’ doesn’t match ‘bid.gas_limit’, we would reject the payload before sending it to the EL anyways right. So the gas limit the EL is verifying is the bids gas limit? 
To me it seems like the gossip check is the invalid one?
[2026-05-11T12:54:22.383000+00:00] bharath.vedartham: The payload gas limit would be the calculated gas limit
[2026-05-11T12:58:43.889000+00:00] nflaig: if `bid.gas_limit` is the calulcated gas limit then we need to fix taht gossip check, but questionable if the CL should have to deal with this, also <@412614104222531604> noted it might become more difficult with EIP-8073? Ideally the CL should only have to deal with the desired/target gas limit
[2026-05-11T13:20:23.588000+00:00] bharath.vedartham: We should discuss this in ACDT
[2026-05-11T15:12:55.571000+00:00] potuz: shit we may have spoken too soon
[2026-05-11T15:13:11.416000+00:00] potuz: we do not store the *actual* gas used on the beacon state anymore
[2026-05-11T15:13:25.057000+00:00] potuz: so doing 1559 in the CL is more problematic
[2026-05-11T15:15:05.782000+00:00] potuz: I will open two PRs, one making the bid be the *actual* gas used and then the CL verifies 1559. Another with the bid being the target gas used and then storing the actual one in the state
[2026-05-11T15:32:45.599000+00:00] nflaig: should I open a 2nd Pr on the engine?
[2026-05-11T15:33:09.419000+00:00] potuz: we can go with just FCU on the engine for now
[2026-05-11T15:33:37.116000+00:00] potuz: I'll open the one requiring the bid to be the *actual* gas limit making the enforcement not a consensus breaking change
[2026-05-11T15:33:53.375000+00:00] potuz: and then we can discuss if we want to move to the bid being the preferences as discussed in the call
[2026-05-11T15:34:26.023000+00:00] nflaig: https://github.com/ethereum/execution-apis/pull/796 is open
[2026-05-11T15:34:39.638000+00:00] nflaig: let's see if they still ignore me 🙂
[2026-05-11T15:35:15.052000+00:00] tersec: specifying the target in fcuV4 seems distinct conceptually from the CL-verification of it
[2026-05-11T15:35:26.005000+00:00] tersec: and the 1559 in CL is only salient for latter
[2026-05-11T15:35:33.031000+00:00] nflaig: it is completely unrelated
[2026-05-11T15:35:41.811000+00:00] jtraglia: Forgive my ignorance. Doesn't the actual gas limit change each slot? How would a proposer know what the gas limit will be ~2 epochs ahead of their proposal slot?
[2026-05-11T15:36:21.359000+00:00] tersec: I'm still not really convinced that pulling 1559 into the CL just for some gossip verification makes sense but that's a separate thing
[2026-05-11T15:36:28.701000+00:00] nflaig: the preference has the target
[2026-05-11T15:36:43.742000+00:00] jtraglia: Ahhh
[2026-05-11T15:36:51.452000+00:00] jtraglia: The preference will have the target & the bid will have the actual.
[2026-05-11T15:37:02.251000+00:00] jtraglia: Okay that makes more sense.
[2026-05-11T15:37:04.826000+00:00] nflaig: we aren't really pulling it in, I suspect every client has EIP-1559 imlemented today, otherwise how do you check the execution payload header you get from builder?
[2026-05-11T15:37:35.602000+00:00] potuz: me neither, but the option (which I will open as well) is to store the actual gas used in the beacon state
[2026-05-11T15:37:39.482000+00:00] potuz: which is also not clean
[2026-05-11T15:38:06.827000+00:00] potuz: another option is what Nico says of sending the target on new payload and asking the EL to check it
[2026-05-11T15:38:16.748000+00:00] potuz: but I learned not to request too many things from the EL
[2026-05-11T15:38:26.792000+00:00] potuz: also it is really a consensus concern
[2026-05-11T16:28:22.364000+00:00] potuz: <@586161934425128960> this is not what we discussed in ACDT, but this is the absolute simplest approach, In case there's a chance that approach 2 in the PR description will be considered I will open that PR
[2026-05-11T16:28:24.187000+00:00] potuz: https://github.com/ethereum/consensus-specs/pull/5236
[2026-05-11T16:41:18.692000+00:00] potuz: checking gas limit
[2026-05-11T17:35:52.528000+00:00] nflaig: EIP-8073 doesn't seem to change anything wrt to the gas limit transition rule
[2026-05-11T17:36:56.908000+00:00] potuz: yep the bootnode, is it running Teku?
[2026-05-11T17:37:45.277000+00:00] potuz: yeah the computation is trivial, but the problem is that we need the parent's gas limit
[2026-05-11T17:38:13.842000+00:00] barnabasbusa: yes
[2026-05-11T17:40:13.770000+00:00] barnabasbusa: [gpo](https://github.com/ethereum/EIPs/pull/11644) solves this
[2026-05-11T18:07:37.790000+00:00] potuz: oh the best thing about these types of PRs is that there aren't any damned tests that get broken 🙂
[2026-05-11T18:08:06.113000+00:00] nflaig: because we don't have gloas gossip tests 😂
[2026-05-11T18:08:16.292000+00:00] jtraglia: soon (tm)
```

</details>
