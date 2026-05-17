# PR 5094 Epoch Transition and Fork Choice Changes

**Channel:** epbs | **Date:** 2026-04-15

## Summary

The discussion centers on PR 5094's impact on epoch transitions and fork choice behavior in the Ethereum consensus layer. nflaig argues that the PR doesn't fundamentally change the existing FCU (forkchoiceUpdated) call patterns and highlights a potential benefit: decoupling epoch transitions from payload timing, which could allow more flexible `PAYLOAD_ATTESTATION_DUE_BPS` configuration and support future slot time reductions. However, potuz raises a significant concern that PR 5094 introduces a new ambiguity where nodes can no longer determine their exact fork choice head from the head state alone - they now need additional off-chain information to know whether to build on "full" or "empty" blocks.

potuz emphasizes this represents a substantial departure from the current behavior where the head state unambiguously identifies what fork choice selected, a property that has existed since phase 0. While he acknowledges no concrete attacks emerge from this change and that FOCIL will eventually remove similar guarantees anyway, he expresses discomfort with losing the current property where "knowing the head state is equivalent to knowing our head." tbenr agrees this ambiguity is a new introduction. The discussion reveals tension between operational benefits (decoupled epoch transitions) and architectural concerns about maintaining clear fork choice semantics.

## Participants

- nflaig
- potuz
- tbenr

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-15T09:51:16.016000+00:00] nflaig: I think this is not true, or at least my PR doesn't change anything here

so there are 2 FCU calls
- 1. without payload attributes
- 2. with payload attributes if we are the next proposer

with my spec change, (1) doesn't change to current gloas, and (2) also doesn't change because even pre-gloas you need to do an epoch transition (`process_epoch`) before you call FCU with payload attributes since it affects `get_expected_withdrawals(state).withdrawals`
[2026-04-15T09:58:07.673000+00:00] nflaig: 
[2026-04-15T09:59:41.543000+00:00] nflaig: the only difference I see between pre-gloas and gloas with #5094 is that you have to run `apply_parent_execution_payload`
[2026-04-15T10:39:38.367000+00:00] nflaig: ok so this actually seems like a major benefit of #5094 is that we decouple the epoch transition from the payload if we assume the worst case with `PAYLOAD_ATTESTATION_DUE_BPS = 7500` means we get the payload at ~9 seconds (current mainnet slot duration), this might allow us to choose `PAYLOAD_ATTESTATION_DUE_BPS` more flexibly as epoch transition time is no longer a consideration. I don't think ~3 seconds is a big deal right now, but if we wanna further reduce slot time this can be a major benefit, also the general trend seems to be that each fork we add more work to the epoch transition
[2026-04-15T11:47:54.321000+00:00] potuz: Fwiw Vitalik correctly noticed that this property will anyway be lost by FOCIL
[2026-04-15T13:31:50.193000+00:00] nflaig: isn't this already the case in current gloas spec, `payload_states` is a fork choice internal that needs offchain information to construct a block because you can either pick `block_states[root]` or `payload_states[root]` but to know which of these you need fork choice, I think my PR just makes this more visible and evident that the execution payload with epbs becomes a pure DA concern based fork choice view

> "A node today is given a beacon state and it doesn't need anything else to construct a block that builds on it and is automatically canonical. "
I don't even think that is defined in current gloas spec, kinda related to the same I commented [here](https://github.com/ethereum/consensus-specs/pull/5094#issuecomment-4252338022)

> node needs to have external information to tell it if it's block needs to build on full or empty
this part specifically is true in both current gloas and after 5094, it's just differently expressed inside the spec, ie. before we store that intermediate information as a post-payload state, with 5094, we store the block state only and the execution payload separately, but either way to make sense on whether to build on full or empty, you need fork choice
[2026-04-15T13:38:19.910000+00:00] potuz: no it's not, the head state is either the post-EL state advanced to the current slot or the post-CL state advanced to the current slot. Given such a state you know exactly on top of what you are proposing. If it is a post EL-state you build on full and you have everything to propsoe already, if it is a post-CL state you build on empty and you have all that you need already. The head state is all that is needed to prepare a block.
[2026-04-15T13:39:20.139000+00:00] potuz: forkchoice is always needed to choose the head state, this is true before Gloas, the point is that the current Gloas spec, given the head state, you don't need anything else for liveness, but post 5094 that's no longer true
[2026-04-15T13:39:24.588000+00:00] nflaig: > the head state is either the post-EL state advanced to the current slot or the post-CL state advanced to the current slot
sure but how do you decide which one to pick without offchain/fork choice info?
[2026-04-15T13:39:45.997000+00:00] potuz: this has always since phase 0 been the case: forkchoice tells you which staate is the head state
[2026-04-15T13:44:20.219000+00:00] nflaig: but head state in current gloas is ambigious without fork choice, it can be either `block_states[root]` or `payload_states[root]`

I guess your "given a head state"  is what I find misleading, you mean a state you feed into a node at startup, like checkpoint sync?
[2026-04-15T13:46:08.052000+00:00] potuz: it's not, head state has always been the post-state chosen by forkchoice, advanced to the current slot, this hasn't changed since phase0 and this is still the case in the current Gloas, with or without 5094. The difference is that with 5094, given that state alone, you cannot recover what forkchoice told you was your head: you lost the info if forkchoice had told you full or empty and you need to recover it offchain. Whether the current spec, if you give me the head state I can pinpoint exactly what forkchoice head was pointed at
[2026-04-15T13:46:37.483000+00:00] potuz: there is no more ambiguity in the current spec than in phase0, 5094 does bring this ambiguity.
[2026-04-15T15:16:37.669000+00:00] tbenr: I agree, this is new.
[2026-04-15T15:49:02.482000+00:00] potuz: It is a substantial change  that somehow it gives me the creeps, but 1) I can't find any real attack over it and 2) <@273808422753796097> pointed that FOCIL anyway removes the "static" feature above and we need offchain info to be able to construct a canonical block anyway. Still, even FOCIL does not remove this feature today that we have that knowing the headstate is equivalent to knowing our head. 5094 does remove this.
```

</details>
