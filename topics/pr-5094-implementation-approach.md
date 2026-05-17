# PR 5094 discussion and implementation approach

---

## 2026-04-10 (epbs)

The discussion centers around PR 5094, which proposes changes to how execution payloads and requests are handled in Ethereum consensus. The key technical innovation is including commitments as part of the finalized state rather than requiring the full payload for validation. This approach would allow nodes to sync block 33 without needing the payload of block 31, and potentially enable optimistic chain syncing without requiring the `ExecutionPayloadEnvelope`.

A critical implementation detail emerged around ensuring that requests match the commitment in the bid during payload processing. Without this validation, there's a risk of chain deadlock where no proposer can build on a full block, requiring the payload to be marked as invalid. The participants confirmed this check exists in the PR but discussed its proper placement, with validation occurring in the forkchoice handler rather than just in `process_execution_payload`.

The proposal received positive reception, with tbenr calling it "a big improvement." However, there are differing views on implementation complexity - while nflaig noted it would reduce complexity in Lodestar's state cache (similar to current mainnet behavior), potuz expressed concerns about potential caching complications in Prysm, though acknowledged it may not be a blocker. Potuz committed to implementing the changes to better understand the practical implications.

**Participants:** nflaig, potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-10T12:56:59.813000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5094 (unless I used some evil spec sorcery it seems possible)
[2026-04-10T13:07:43.709000+00:00] potuz: can you tl;dr me instead of reviewing the code first? how do you sync 33 as asked above without the payload of 31? how do you verify the requests?
[2026-04-10T13:08:50.217000+00:00] potuz: do you commit in the bid?
[2026-04-10T13:09:12.922000+00:00] potuz: like here?
[2026-04-10T13:10:23.505000+00:00] nflaig: you have the commitment as part of the finalized state, I don't see why you even need the payload
[2026-04-10T13:10:47.341000+00:00] potuz: yeah that's why I asked if you commit in the bid
[2026-04-10T13:12:42.348000+00:00] nflaig: and I think with that change you can even optimistically sync the chain without requiring the `ExecutionPayloadEnvelope`
[2026-04-10T13:13:16.547000+00:00] potuz: you need the hash validation if we want to keep the same assurances as today
[2026-04-10T13:23:35.347000+00:00] potuz: left a comment, I think you need to enforce when processing the payload that the requests match the commitment in the bid, otherwise there's a chain deadlock, since no proposer can build on full.  The payload needs to be marked as invalid
[2026-04-10T13:25:34.481000+00:00] tbenr: I like the approach and I believe it is a big improvement.
[2026-04-10T13:26:28.474000+00:00] nflaig: agree, I have that check, but it might be in the wrong place
[2026-04-10T13:26:51.124000+00:00] potuz: I'll take a stab at implementing the changes, I *suspect* it'll make our caching harder as mentioned above, but not clear that it's a blocker for us
[2026-04-10T13:27:20.436000+00:00] potuz: oh I only checked in `process_execution_payload`
[2026-04-10T13:29:26.930000+00:00] potuz: oh yeah you have it on the forkchoice handler
[2026-04-10T13:29:51.168000+00:00] nflaig: you said that before, but for lodestar it removes a lot of complexity in our state cache, essentially what we have on mainnet now
[2026-04-10T13:30:33.519000+00:00] nflaig: so I am curious why that is for prysm, having to worry about 2 states per slot per root seem just more complex to me
[2026-04-10T13:31:04.252000+00:00] potuz: I think it's on the NSC, but I may be wrong, will find out when I code it, it doesn't look too bad
```

</details>

---

## 2026-04-13 (epbs)

The discussion centers around PR 5094, which appears to have strong support from most client teams and is being considered for epbs-devnet-2. The participants are coordinating the testing and release timeline, with jtraglia proposing a process to merge the PR, generate nightly reference tests, and confirm client compatibility before making a release within 48 hours if approved.

However, potuz emphasizes the need for thorough review before rushing to merge, noting that all subtleties should be covered despite the general consensus. terencechain has created a draft branch and is willing to test against nightly references as a sanity check, though passing tests wouldn't be a signal to rush the merge process.

Key action items include nflaig posting a document detailing the benefits and trade-offs of the change, conducting thorough PR reviews, and making a decision during the ACDT call. The team is balancing the desire for quick progress with the need for careful technical review of this significant change.

**Participants:** barnabasbusa, jtraglia, nflaig, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-13T13:28:16.960000+00:00] barnabasbusa: got a branch for it?
[2026-04-13T13:28:25.282000+00:00] barnabasbusa: I feel like this is epbs-devnet-2 terretory
[2026-04-13T13:28:31.408000+00:00] barnabasbusa: I think we can make the call on this today on acdt
[2026-04-13T13:28:48.288000+00:00] barnabasbusa: but based on what I have read above, it seems like most client teams are strongly in favor of this change.
[2026-04-13T13:31:01.142000+00:00] terencechain: Yes I agree. I'll push once I get Infront of computer
[2026-04-13T13:31:32.907000+00:00] barnabasbusa: <@592004585506340885> if we do decide in favor of 5094, how soon can we get a new cl spec version out?
[2026-04-13T13:48:15.624000+00:00] terencechain: https://github.com/OffchainLabs/prysm/pull/16658
[2026-04-13T14:04:26.823000+00:00] jtraglia: If we decide to merge this, I think we can get a release out in < 48 hours. I would like to (1) merge the PR, (2) generate nightly reference tests for it, and (3) confirm that one or more clients pass the updated tests. Then, if successful, we'll make a release.
[2026-04-13T15:03:58.920000+00:00] jtraglia: Sorry, the end of the breakout call was very rushed. If there are any concerns/comments with the timeline for merging that PR & making a release, I'd be happy to chat about it here.
[2026-04-13T15:08:36.813000+00:00] nflaig: I will post a document with other wins we get from this change + trade-offs later today/tomorrow, it seemed though that everyone in the call had a good understanding of those already
[2026-04-13T15:11:48.455000+00:00] potuz: I believe we need a release before passing tests :). I will focus on reviewing the PR and we should all do that, but please lets not rush on merging it until we've covered all subtleties.
[2026-04-13T15:15:05.192000+00:00] jtraglia: Yeah I won't rush it. But it would be nice if a single client passed tests, as a sanity check.
[2026-04-13T15:23:38.650000+00:00] terencechain: if you give me the nightly reference, i can see if my draft branch passes it, but even prysm passes it, it's not a signal to rush it
[2026-04-13T18:12:43.900000+00:00] nflaig: #5094 review lounge
```

</details>

---

## 2026-04-14 (epbs)

potuz identifies a fundamental shift in state management introduced by EIP-5094, where the traditional distinction between full and empty states becomes blurred, causing the concept of "head state" to lose its clear meaning. This change breaks a core assumption that has existed until now: that a node given only a beacon state can independently construct a canonical block building on that state.

Under 5094, nodes require external, off-chain information to determine whether their block should build on a full or empty state, and in cases where it needs to build on full state, the node must also have access to the complete requests. While potuz doesn't believe this creates liveness risks, they emphasize this represents a consequential architectural change to how Ethereum nodes operate and construct blocks.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-14T19:34:48.407000+00:00] potuz: I think a distilled form of the discussion in threads is that the most fundamental difference in the spec that 5094 brings is that a state doesn't discern between full or empty so the notion of "head state" loses some meaning. This is evidenced by the fact that we now need offchain information to construct a block. That is. 

"A node today is given a beacon state and it doesn't need anything else to construct a block that builds on it and is automatically canonical. "

This is no longer true with 5094 because the node needs to have external information to tell it if it's block needs to build on full or empty. And in case it needs to build on full it requires to have the full requests also available. 

I don't think this is a risk to liveness, but it is a consequential change
```

</details>

---

## 2026-04-15 (epbs)

The discussion centers on PR 5094's changes to Fork Choice Updates (FCU) and epoch transitions in the Ethereum consensus layer. nflaig argues that the PR doesn't fundamentally change FCU behavior since both pre and post-Gloas implementations require epoch transitions before calling FCU with payload attributes due to withdrawal calculations. They highlight a key benefit: decoupling epoch transitions from payload processing, which could allow more flexible timing for `PAYLOAD_ATTESTATION_DUE_BPS` and better support for reduced slot times as epoch transition complexity grows.

However, potuz and tbenr raise concerns about a substantial architectural change introduced by PR 5094. In the current Gloas specification, given a head state, a node has complete information to construct a canonical block and knows exactly what it's building on (full or empty). With PR 5094, this property is lost - nodes would need additional off-chain fork choice information to determine whether to build on full or empty blocks, as the head state alone becomes ambiguous between `block_states[root]` and `payload_states[root]`.

While potuz acknowledges they cannot identify specific attacks exploiting this change and notes that FOCIL will eventually remove similar properties anyway, they express concern about losing the current guarantee where knowing the head state is equivalent to knowing the complete fork choice head. The discussion reveals tension between the practical benefits of decoupling epoch transitions from payload timing versus maintaining the existing architectural property of state self-sufficiency.

**Participants:** nflaig, potuz, tbenr

<details>
<summary>Raw messages</summary>

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

