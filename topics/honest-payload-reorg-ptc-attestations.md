# Honest payload reorg feature and PTC attestations

---

## 2026-05-02 (epbs)

Potuz proposed adding an "honest payload reorg feature" to the Ethereum specification rather than leaving it as an implementation detail. The feature would allow reorganization of payloads that receive late attestations from the Payload Timeliness Committee (PTC). 

The motivation stems from current performance issues where payloads are taking significantly longer to execute, with some cases reaching 13 seconds at 150M gas limit, causing execution to spill into the next slot. Potuz suggests that by specifying this reorg mechanism, pricing could be used to prevent payloads from executing beyond their allocated slot time. The proposal appears to be directed at a specific team member for their input, but no response or consensus was captured in this message thread.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-02T14:16:39.083000+00:00] potuz: <@520034910149410861> how do you feel about putting the honest payload reorg feature in the spec to reorg payloads that were attested late by the PTC, instead of leaving it as implementation detail? 

With this we can use pricing to prevent the Payload executing into the next slot. At 150M we are seeing some Payloads taking 13 seconds in some cases
```

</details>

---

## 2026-05-04 (epbs)

Based on the single message provided, this discussion appears to have just begun with brechy mentioning `on_payload_attestation_message` assertions. The message is extremely brief and only references what seems to be a function or method name related to payload attestation message handling in the Ethereum protocol.

No technical details, decisions, conclusions, or action items can be determined from this single introductory message. The discussion would need additional messages to provide any meaningful technical context about what specific assertions or issues are being addressed regarding payload attestation messages.

**Participants:** brechy

<details>
<summary>Raw messages</summary>

```
[2026-05-04T15:26:41.709000+00:00] brechy: `on_payload_attestation_message` assertions
```

</details>

---

## 2026-05-06 (epbs)

This discussion focused on implementing "honest PTC (Payload Timeliness Committee) reorgs" to make proposer behavior more rational when there's a mismatch between the proposer's view and the PTC's vote on payload availability or timeliness. The core issue is that currently, if a proposer has payload data but receives overwhelming PTC votes indicating data unavailability, the proposer still builds on the full payload, which is irrational since attesters in the next slot would likely agree with the PTC and cause a reorg anyway.

The team clarified that the mechanism works through the existing `should_extend_payload` function, where if the PTC votes against payload availability/timeliness, attesters will naturally follow the PTC view and not extend the payload. However, there are edge cases where problems can still occur - specifically when the PTC votes for availability but execution takes too long or blobs arrive late into the next slot, potentially causing proposer reorgs. Francesco provided key guidance that proposers should only reorg when there's a majority of negative (not absent) PTC votes, ensuring attesters will share the negative opinion by including those votes in their blocks.

As action items, potuz opened PR #5210 for the basic implementation and PR #5212 for dual deadlines enforcement. The team agreed that for data availability cases the reorg can be done unconditionally, but for payload reorgs additional conditions should be considered such as ensuring the chain is finalizing and the head is from the previous slot. Importantly, there's no proposal to force attesters to reorg based on PTC - this mechanism only affects proposer behavior.

**Participants:** bharath.vedartham, fradamt, m.kalinin, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-06T08:38:18.468000+00:00] bharath.vedartham: regarding honest PTC reorgs, do we also want to cover a case where if a proposer has the payload but majority of the PTC votes say that the payload is unavailable, then the proposer reorgs the payload? 
Here: https://github.com/ethereum/consensus-specs/pull/5186 we seem to only cover the case if the payload is available but the PTC voted for the DA to not be available. 
Do we also want to specify the case if payload is available but PTC has neither voted for payload availability or DA availability, the the proposer reorgs?
[2026-05-06T08:48:10.467000+00:00] m.kalinin: Could anyone please explain me why do we want https://github.com/ethereum/consensus-specs/pull/5186 ?
Shouldn't an ultimate decision of the DA be after attesters of the next slot? Moreover, under synchrony if proposer witnesses DA then attesters of the next slot will do as well
[2026-05-06T10:09:06.240000+00:00] potuz: Today, if you are a proposer that has seen your 4 columns that you need to hold, so your view of DA is fine for that block. But you receive from the PTC an overwhelming vote that DA is missing. Our current forkchoice still makes the proposer build on top of full. This is not a rational thing to do as it is highly likely that the attester set would agree on DA with the PTC. That PR is there just to make the proposer do the rational thing and reorg the payload in this case, avoiding an unnecessary reorg and avoiding the chain missing one extra payload for the next slot.

FWIW, today I'll add yet another similar one for the proposer to even reorg the payload if the PTC is voted that it was not timely.
[2026-05-06T10:10:48.763000+00:00] potuz: yes, I'll add this PR soon (although perhaps Nico may beat me to it). I was waiting for Francesco to give his OK, look here https://discord.com/channels/595666850260713488/874767108809031740/1500789266274320384
[2026-05-06T10:27:40.753000+00:00] m.kalinin: > it is highly likely that the attester set would agree on DA with the PTC

when does attester set decide on DA?
[2026-05-06T10:38:31.753000+00:00] potuz: On the next slot, so after the proposer has proposed his block.
[2026-05-06T10:46:44.639000+00:00] m.kalinin: So, this implies that blobs need to be propagated before the block of the next slot? I think given that proposer is honest and synchrony holds, this should happen in the majority of the cases
[2026-05-06T11:10:40.151000+00:00] bharath.vedartham: i m trying to understand when the proposer calls FCU with honest PTC reorgs, if i m right:
1. We call FCU with head block hash with  `payload.bid.block_hash` when the payload is received.
2. If we then realise that the PTC has not voted for this payload, we again call FCU with head block hash as   `payload.bid.parent_block_hash`  causing the EL head to be rolled back?
[2026-05-06T11:10:43.921000+00:00] m.kalinin: I admit that we have a more complicated blob propagation since Fulu. But I am wondering won't it turn into a problem if proposer build on empty block? Won't its block be reorged out because the majority of attesters in fact received missing columns?
[2026-05-06T11:19:07.999000+00:00] potuz: No, attesters follow PTC if the proposer reorgs. That's the whole point of the PTC
[2026-05-06T11:19:49.952000+00:00] potuz: Yes, that's allowed with the new API.
[2026-05-06T11:20:55.786000+00:00] potuz: Alternatively the node may optimize by not calling FCU as we do today for honest reorgs. This shouldn't be specified and left for implementers
[2026-05-06T11:22:55.283000+00:00] m.kalinin: I see, then it should be fine. How is this property enforced?
[2026-05-06T11:25:02.488000+00:00] potuz: I don't understand, how is forkchoice enforced?
[2026-05-06T11:27:19.057000+00:00] potuz: oh no wait I think I missunderstood your comment
[2026-05-06T11:27:43.786000+00:00] potuz: Our current forkchoice has two problems with regards to validation into the next slot
[2026-05-06T11:27:46.630000+00:00] m.kalinin: is there an explicit fork choice case for attesters to follow the PTC  when proposer reorgs a payload?
[2026-05-06T11:28:18.911000+00:00] potuz: If the payload executes into the next slot then attesters can reorg the next block
If the DA arrives into the next slot then attesters can reorg the next block
[2026-05-06T11:29:27.318000+00:00] potuz: I believe these two are just things that we left to fix later into enforcing the PTC view if the proposer wants to reorg, but I think <@520034910149410861> never liked to add these into forkchoice. I will ipen the respective PRs nonetheless when Cayman's PR counting Nay votes gets merged
[2026-05-06T11:32:54.626000+00:00] m.kalinin: I don't like it either as then we give PTC power to reorg payloads
[2026-05-06T11:34:35.162000+00:00] potuz: I do not know if there is a specific test for this, but this is currently done in 
```
def should_extend_payload(store: Store, root: Root) -> bool:
    if not is_payload_verified(store, root):
        return False
    proposer_root = store.proposer_boost_root
    return (
        (is_payload_timely(store, root) and is_payload_data_available(store, root))
        or proposer_root == Root()
        or store.blocks[proposer_root].parent_root != root
        or is_parent_node_full(store, store.blocks[proposer_root])
    )
```
The first line is FALSE if the PTC votes against the payload
The second line is false if the proposer proposed early
The third line is false cause the proposer is building on the parent (just empty)
The last one is false cause it's building on empty
[2026-05-06T11:34:54.149000+00:00] potuz: So the usual forkchoice run for the attester will make it follow PTC if the proposer decides to reorg
[2026-05-06T11:36:12.207000+00:00] potuz: the problem I am signalling above happens when the PTC votes for timeliness or availability of a payload, but execution last until the next slot or blobs arrive only later into the next slot
[2026-05-06T11:36:42.193000+00:00] potuz: I hope this explains it <@425572898787426305>
[2026-05-06T11:37:02.043000+00:00] m.kalinin: thanks a lot for the explanation let me wrap by head around it
[2026-05-06T11:47:07.938000+00:00] nflaig: we merged https://github.com/ethereum/consensus-specs/pull/5180 almost a week ago
[2026-05-06T11:47:30.682000+00:00] potuz: loool, I should check the repo more often, I'll rebase and open a couple of PRs then
[2026-05-06T12:21:40.918000+00:00] m.kalinin: > If the DA arrives into the next slot then attesters can reorg the next block
won't attesters in this case adhere to PTC view on DA, i.e. `(is_payload_timely(store, root) and is_payload_data_available(store, root))` is False?
[2026-05-06T12:28:27.391000+00:00] fradamt: As far as I am concerned, it should be fine for the proposer to reorg as long as you have a majority of negative (not absent) votes, so that you can be sure that attesters will share your negative opinion (by including those votes in the block)
[2026-05-06T12:32:31.190000+00:00] potuz: Thanks. We just needed this sentence 🙂
[2026-05-06T12:34:02.439000+00:00] potuz: Yes, the problem happens in the other situation: the PTC voted for present on a split 51%, proposer didn't have DA and at proposal time so builds on empty. Arresters get 100% DA into the slot
[2026-05-06T12:34:17.282000+00:00] potuz: The proposer gets reorged in this case.
[2026-05-06T12:34:58.365000+00:00] potuz: There's the issue if attesters should vote for DA or timely DA, but I think that one is more dangerous so leaving as-is is fine
[2026-05-06T12:36:00.956000+00:00] potuz: This is the closest analog of timely but executed into the next slot
[2026-05-06T12:36:06.995000+00:00] m.kalinin: > This function is
> similar to `should_extend_payload` but it takes into consideration the PTC view
> on DA

`should_extend_payload` calls `is_payload_data_available`, proposer cannot call `should_extend_payload`, this is why that PR?
[2026-05-06T12:38:31.904000+00:00] potuz: Because should extend payload always returns true
[2026-05-06T12:38:42.636000+00:00] m.kalinin: i see now thank you
[2026-05-06T12:44:40.370000+00:00] fradamt: just to clarify, there's no proposal to force *attesters* to reorg based on the PTC?
[2026-05-06T13:09:48.816000+00:00] potuz: definitely not
[2026-05-06T13:10:35.398000+00:00] potuz: Added this as the most naive version, https://github.com/ethereum/consensus-specs/pull/5210

However, I think for the DA one is fine to do this unconditionally, but for the payload I think we should also add the conditions that the chain is finalizing and that the head is from the previous slot at least.
[2026-05-06T16:43:04.761000+00:00] potuz: opened another one for dual deadlines. It is very simple and the benchmarking team is already looking into setting these parameters correctly so I think we should be already in a position to enforce them
[2026-05-06T16:43:23.125000+00:00] potuz: https://github.com/ethereum/consensus-specs/pull/5212
```

</details>

