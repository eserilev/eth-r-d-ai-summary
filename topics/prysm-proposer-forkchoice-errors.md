# Prysm proposer errors and forkchoice state issues

---

## 2026-03-18 (epbs)

The discussion centers on a critical issue causing Prysm proposers to fail when building blocks due to "invalid forkchoice state" errors from Geth. The error manifested as Geth rejecting forkchoice updates because the "final block not in canonical chain," preventing Prysm validators from successfully proposing blocks at slot 97539.

Potuz identified the root cause: the finalized hash passed to the engine API should always be the *parent hash* of the block with the finalized root, not the hash of the finalized block itself. This same principle applies to safe hashes and will extend to future fast confirmation rules. The issue stems from Prysm incorrectly returning block hashes that Geth doesn't recognize as valid in the forkchoice state.

Terencechain confirmed this implementation bug was the main reason Prysm was missing blocks periodically, while other consensus clients appeared to have implemented the hash specification correctly. An action item was noted for updating the engine API specification if the current hash requirements aren't clearly documented.

**Participants:** potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-18T03:40:51.923000+00:00] terencechain: heads up, all the prysm proposers are down with the following error, im looking into it, and cc <@755590043632140352> so he is aware when he wakes up
```
[2026-03-18 03:39:47.06]  INFO rpc/validator: Begin building block sinceSlotStartTime=61.754201ms slot=97539
[2026-03-18 03:39:47.11] DEBUG rpc/validator: Payload ID cache miss headRoot=0x72b7070b740065986645194a575141e52afc4bf89b86935f03213d4f4b4e5659 slot=97539 validatorIndex=794
[2026-03-18 03:39:47.11] ERROR rpc/validator: Finished building block error=rpc error: code = Internal desc = Could not get local payload: could not prepare payload: invalid forkchoice state sinceSlotStartTime=116.9942ms slot=97539 validator=794
```
[2026-03-18T03:42:58.138000+00:00] terencechain: Geth complains with
```
WARN [03-18|03:42:12.665] Final block not in canonical chain       number=88687 hash=3a3274..f267ce
WARN [03-18|03:42:12.665] Served engine_forkchoiceUpdatedV3        conn=[2a01:4f8:1c19:38ee::8]:59638 reqid=30488 duration=1.310008ms err="Invalid forkchoice state" errdata="{\"err\":\"final block not in canonical chain\"}"
```
[2026-03-18T20:44:49.123000+00:00] potuz: I'll leave this here cause it's causing confusion even internally. The finalized hash that is passed to the engine should always be the *parent hash* of the block with finalized root. While it's technically possible to track if validators where using full or empty to vote for target, we're not doing it. We only finalize the pending node, so it's parent hash is the only one with a guarantee to be final
[2026-03-18T20:46:05.389000+00:00] potuz: Same thing applies to the safe hash and the same thing will happen to the fast confirmation rule
[2026-03-18T20:47:10.517000+00:00] potuz: This is independent of missing slot zero or whatever, it's always parent hash of the CL block with the given root
[2026-03-18T21:08:13.249000+00:00] potuz: BTW <@425572898787426305> this should be changed if the hashes are currently specified in the engine API
[2026-03-18T22:01:25.160000+00:00] terencechain: This is the main reason Prysm missed blocks periodically, we returned an incorrect block hash that Geth didn't agree with (i.e. "invalid forkchoice state"). So far it doesn't seem like other clients have the same issue, so they may have implemented it correctly.
```

</details>

---

## 2026-04-07 (epbs)

The discussion centers around two interconnected bugs: a forkchoice bug and an API bug related to checkpoint states and payload handling. Potuz advocates for treating these as separate issues, suggesting the forkchoice bug can be fixed by removing the checkpoint state cache (which is a Python optimization artifact) and using `get_ancestor` calls instead, without requiring consensus changes. Tuyen proposes tracking `finalized_checkpoint_payload_status` to prevent payload reorgs once a checkpoint is finalized, but Sproul strongly opposes this approach, arguing it makes finalization subjective and could weaken crypto-economic guarantees by allowing network splits without slashing.

Sproul initially identifies a related issue with justified balances where the justified checkpoint alone may not uniquely determine balances when payloads are missing, but later acknowledges this GitHub issue may be invalid after further discussion. The conversation reveals implementation complexity around slot 0 FFG votes and checkpoint sync semantics, with concerns about how to handle state advancement when payloads are only justified rather than finalized.

The discussion concludes with Potuz strongly opposing any consensus rule changes, emphasizing that the current problems stem from API implementation details and Python optimizations rather than fundamental consensus issues. He warns that proposed solutions involving payload transition changes could introduce unforeseen problems with builder/proposer incentives, engine API calls, and syncing complexity that would require extensive safety analysis. Sproul appears to move toward Potuz's position by the end of the discussion.

**Participants:** 0xunclebill, nflaig, potuz, sproul, tbenr, tuyen4818

<details>
<summary>Raw messages</summary>

```
[2026-04-07T00:39:54.166000+00:00] potuz: Can we break this problem in two? There's a forkchoice bug and an API bug. I won't make any statements about the API bug but I want to fix the forkchoice one. To fix the forkchoice one we do not need to change any checkpoint type and in fact we can just remove the whole checkpoint state cache that is an artifact that leaks unnecessarily into implementations. We should have calls to get_ancestor and if spec maintainers want to optimize this by having an internal cache that is not leaked into the markdown that's fine
[2026-04-07T03:07:48.207000+00:00] tuyen4818: https://github.com/ethereum/consensus-specs/pull/5073
the concern is about the payload of the skipped slot finalized checkpoint could be reorged
but that's also something we want to avoid, to ensure forkchoice to have as less reorg as possible
here I propose a candidate:
- whenever a checkpoint is promoted to be finalized, also pin its payload_status, tracked as a new field `finalized_checkpoint_payload_status` in Store
- reject blocks if it's NOT derived from that finalized checkpoint payload status

so it should be certain to know which checkpoint state to be used as `finalized` endpoint
please let me know your ideas cc <@755590043632140352> <@534934855113506836> <@586161934425128960> <@358120958726373381> <@602753420033785856> <@520034910149410861>
[2026-04-07T04:13:13.393000+00:00] sproul: I don't like this solution, because it changes the definition of finalized to be subjective and based on the node's local view. Even though it's unlikely to be divergent.
[2026-04-07T04:26:35.684000+00:00] tuyen4818: yeah that's a true concern
but first do you agree with that approach to not allow reorg the payload once a new finalized checkpoint is known?
we can get from finalized_checkpoint_payload_status from BeaconState instead I think, that should be safer
[2026-04-07T04:27:21.078000+00:00] sproul: No, I don't agree with that approach. The payload is not actually finalized unless we change the definition of Checkpoint to represent that
[2026-04-07T04:28:42.691000+00:00] sproul: If we adopt anything like this, then it's possible for two nodes to have different ideas of "finalization" _without_ anyone getting slashed. It weakens the whole crypto-economic guarantee of finalization. I'm worried that an attacker could try to exploit this to create a permanent network split that could only be resolved manually (by checkpoint syncing half the network to the "correct" side)
[2026-04-07T04:31:11.763000+00:00] sproul: <@688748669268132001> has also realised that this ambiguity about payload presence creates an issue with the justified balances too. The "justified checkpoint" is no longer sufficient to uniquely determine the justified balances, so fork choice views can diverge (a lot) between nodes that have/don't have the justified block's payload. E.g. imagine justified block in epoch 1 is justified at epoch 20 (skips between epoch 1 and 20). The payload from this block is capable of (greatly) influencing the balances of validators at epoch 20
[2026-04-07T04:44:12.854000+00:00] sproul: Issue for the justified balances issue here: <https://github.com/ethereum/consensus-specs/issues/5074>

With this I am leaning towards just copping the change to the Checkpoint type. It feels like the conceptually correct change to make, with less tech debt.
[2026-04-07T07:30:49.297000+00:00] nflaig: found the same issue yesterday, we discussed a few approaches internally but none seems to quite be right, see [summary](https://gist.github.com/lodekeeper/5da112623d62f9ee270130090a7d3f19).

> With this I am leaning towards just copping the change to the Checkpoint type
how would you deal with slot 0 FFG vote?
[2026-04-07T07:37:51.161000+00:00] sproul: Not sure how to deal with the slot 0 case, it's a bit terrible either way. We can't count it towards the full checkpoint, but also don't want to penalise those slot 0 attesters. Maybe they could be assessed for rewards based on their block_root + epoch only, but excluded from the FFG calculations :\
[2026-04-07T07:51:36.581000+00:00] 0xunclebill: so we'd lose 2 slots worth of validator balance from finalizaton. instead of 100% of validator balance, 15/16 of  total active validator balance would be voting to finalize?
[2026-04-07T07:54:48.523000+00:00] tbenr: maybe we could introduce a dedicated api, which just exposes finalized state for doing checkpoint sync, and leave the `/eth/v2/debug/beacon/states/finalized` unchanged. the new api will be equivalent to `/eth/v2/debug/beacon/states/finalized` in pre-gloas.
[2026-04-07T07:56:27.224000+00:00] nflaig: I don't think it's possible with the current spec, if you wanna advance the state to epoch boundary you need to apply the payload which is only justified, so the state you return as finalized, actually isn't
[2026-04-07T07:57:25.668000+00:00] tbenr: ye that's true. slot advancing is broken
[2026-04-07T10:48:11.106000+00:00] potuz: This seems to me that is getting out of hand over an API thing. The spec is rather clear that the justified/finalized state is the post CL state to the block with the checkpoint's root. There can't be any issues with justified balances nor any consensus split. There's an API problem with checkpoint sync that has some consumers and there's an issue with the python spec handling of a state cache. I do not see why we would go with dramatic systematic changes over this
[2026-04-07T10:58:23.657000+00:00] potuz: In short I strongly disagree with this: it doesn't make sense to change the consensus rules over what the checkpoint endpoint should return or over a Python optimization instead of using the attestation state for validation.
[2026-04-07T13:09:12.267000+00:00] sproul: That issue is invalid? Maybe we can flesh out exactly why on Github (I tagged you there)
[2026-04-07T13:10:44.272000+00:00] tbenr: yeah indeed, seems all good in teku
[2026-04-07T13:17:27.341000+00:00] potuz: I do not say the issue is invalid: just that the cache itself is a Python optimization to not call `get_ancestor`. The notion of justified/finalized state should be the post CL-state with the current checkpoint tracking, so there should not be any ambiguity as to which state is used. What I disagree is on changing consensus over this (eg. changing the notion  of Checkpoint) as this would be a dramatic change with unforseen problems over something that can be dealt offchain as is the checkpoint state cache in forkchoice
[2026-04-07T13:22:51.958000+00:00] sproul: I think the linked github issue is invalid tho
[2026-04-07T13:23:28.301000+00:00] sproul: and I am coming around to your position
[2026-04-07T13:27:59.042000+00:00] potuz: The checkpoint state cache indeed could be a problem though, <@688748669268132001> is right that it is a source of bugs if client implementers keep different checkpoints for attestation validation for example. I'm pretty sure that Prysm has this as a bug right now, so if we had a test with an entire epoch of missing blocks and the checkpoint payload including exits, I think we would produce the wrong validation.
[2026-04-07T13:37:50.498000+00:00] nflaig: I don't really understand how my proposal changes anything wrt to timing games and attester incentives, it also get rid of `payload_states` altogether keeps checkpoint sync semantics the same (also might affect how we store checkpoint during non finality, so claiming this is "just" on the api isn't clear to me yet)
[2026-04-07T13:48:10.666000+00:00] potuz: what is "just" in the API is the current "problem", it's not a part of consensus. We can solve it without any changes to consensus. 

There are solutions with consensus changes, and yes, removing entirely the payload transition and tying it to the next beacon block is a way. The incentives problems are with regard to collusions builder/proposers, but most importantly an issue arises if the call to new Payload is valid and the processing of the requests is invalid. The payload needs to be rolled back in this case. This can be dealt with in a few ways, anyway any succesful block will have to reorg the payload, but it requires careful changes to the engine calls, since proposers that find out that the requests are invalid they need to send FCU with the parent block hash, thus the problem is that the invalidity of the payload itself is not checked with the payload but rather with the next beacon block which makes syncing also harder. I believe any of these drastic changes over this issue of what state is being returned on the beacon API are unwarranted and come with going back to the drawing board to analyze the safety of the whole consensus changes
[2026-04-07T13:48:17.532000+00:00] potuz: and this is what I strongly opppose
```

</details>

---

## 2026-04-13 (epbs)

The discussion centers on whether `ForkChoiceNode` and its `payload_status` field can be simplified following PR #5094, which removes `payload_states` from the consensus specs. Tuyen4818 argues that since `payload_states` is being removed and `payload_status` appears only used in `get_head()`, the fork choice specification could be significantly simplified to be closer to the "fulu" implementation. However, other participants point out that payload status tracking is still necessary for handling votes across different variants (FULL, EMPTY, PENDING) from both lagging validators and missed slots, with usage extending beyond `get_head()` to functions like `get_proposer_head`.

The conversation reveals different perspectives on implementation approaches, with wemeetagain suggesting that pending/full status could be tracked via parent payload hash comparisons, and potuz emphasizing that `ForkChoiceNode` is just one possible implementation artifact rather than a fundamental requirement. Potuz clarifies that the PR doesn't actually change fork choice behavior - the removal of intermediate payload states is orthogonal to fork choice logic, which still needs to distinguish between empty and full branches regardless of the specific data structure used.

The discussion concludes without a definitive resolution on simplification, but establishes consensus that fork choice must continue supporting vote counting for the three payload variants (FULL, EMPTY, PENDING) across a 64-slot moving window, regardless of whether `ForkChoiceNode` is the optimal implementation approach.

**Participants:** 0xunclebill, potuz, tbenr, tuyen4818, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-04-13T03:51:38.312000+00:00] tuyen4818: with the current gloas spec, `ForkChoiceNode` is mandatory because the same block root addresses two physically distinct states `(block_states[root] vs payload_states[root])` — fork-choice has to be able to express which state a head points to.
but with https://github.com/ethereum/consensus-specs/pull/5094 do we still need `ForkChoiceNode` to carry `payload_status`? I don't see any consumers of it in the spec, `payload_states` is removed
if we also remove `payload_status` in `ForkChoiceNode`, the forkchoice spec is a lot simplified and much more closer to fulu
[2026-04-13T05:31:27.289000+00:00] 0xunclebill: Fork choice still needs a way to express payload status. Without `ForkChoiceNode` I'm not sure it would be able to do so. Where would ptc votes and attestation payload weights land?

I'm seeing `is_supporting_vote` `get_node_children` and
 `get_payload_status_tiebreaker`  read it just to name a few
[2026-04-13T06:25:25.447000+00:00] tuyen4818: it's confusing to me
all of that are used for `get_head()`
and `get_head()` is used for head vote, `payload_status` is used for `attData.index == 1` for skipped slot
then it's used inside `get_head()` again
so the only function is `get_head()`

in term of lodestar's implementation, this design maps to 3 different variants with same `blockRoot + stateRoot`. In the current gloas it makes a lot of sense, I'm just not sure after this PR is applied

there could be some use cases other than `get_head()` that I'm missing? if not it opens a way for simplification
[2026-04-13T06:32:56.246000+00:00] 0xunclebill: in `get_proposer_head` via `is_head_weak` and `is_parent_strong` as well. but yeah maybe there is a way to simplify, though I think the concept of payload status still needs to live somewhere in fork choice
[2026-04-13T06:44:23.520000+00:00] tuyen4818: yes `get_proposer_head` uses `is_head_weak` which uses `get_attestation_score`
but it's not needed in this context, it should work the same to fulu, there is no need to separate FULL vs EMPTY vs PENDING in this specific scenario
[2026-04-13T06:45:51.924000+00:00] tuyen4818: I think a way to look at this forkchoice design is start from fulu instead of the current gloas to make sure we don't overcomplicate things and introduce technical debt for future forks
[2026-04-13T09:22:47.646000+00:00] tbenr: I share confusion.
But in my mental model, don't we need anyway to handle votes for the 3 variants? FC must always have to count votes for lagging validators (not seeing block at N and voting for N-1 root with index) voting for FULL and EMPTY and count votes for the non lagging ones voting for PENDING.  And those votes can be seen scattered along a 64 slots moving window.
Am I correct?
[2026-04-13T09:40:28.099000+00:00] 0xunclebill: also not just lagging validators but missed slots as well
[2026-04-13T09:42:50.400000+00:00] tbenr: sure, I was just emphasizing the non perfect-hipothetical case
[2026-04-13T12:36:47.387000+00:00] wemeetagain: I think now that we have single phase stf, we can track pending/full via parent payload hash and payload hash.
"full" means the parent payload hash is N-1 payload hash
"empty" means parent payload hash is N-2 payload hash
[2026-04-13T12:47:36.195000+00:00] potuz: This is the right way of thinking, instead of focusing on the python implementation aspects of it, one should think on the actual spec that Python is trying to express. ForkchoiceNode is an old artifact of how I chose to implement this in Prysm years ago. I'm pretty sure a gazillion different constructions would work just the same and some may be better adapted to each language. The fact of the matter is that forkchoice needs to pick between empty or full branches, choose whatever you want to use to make that distinction. Today forkchoice uses that construction of ForkChoiceNode to avoid duplication
[2026-04-13T12:51:23.030000+00:00] potuz: This PR does not change forkchoice in any way, the dissapeareance of the intermediate payload state is irrelevant to forkchoice.
[2026-04-13T13:02:04.115000+00:00] tbenr: I think stf is orthogonal to FC.
As Potuz just said, you just have to have a data structure supporting the vote counts of this combinations. and "pending" is a legit third variant.
the absence of an intermediate state has not changed the FC by any means to me.
```

</details>

