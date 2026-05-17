# Payload attestation flags and fork choice behavior

**Channel:** epbs | **Date:** 2025-12-19

## Summary

The discussion centers on the behavior of payload attestation flags (`payload_present` and `blob_data_available`) in Ethereum's fork choice mechanism. The key consensus that emerged is that attesters prioritize their own availability view over the Payload Timeliness Committee (PTC) votes - when attesters have payload locally available, they always enforce this view and disregard PTC votes, but when they don't have payload locally available, they vote according to PTC votes they receive. The `payload_present` flag appears to have stricter fork choice enforcement (with a majority threshold), while `blob_data_available` serves more as a hint to proposers with potentially different thresholds.

There's ongoing uncertainty about the exact fork choice implementation, with fradamt noting that fork-choice changes posted previously still need to be incorporated into the main specification. The discussion also touches on the tradeoff between chain liveness and potential builder centralization, with potuz suggesting that attesters should favor liveness by attesting to blocks even when the PTC votes against payload timeliness, as long as the payload can be validated locally.

**Open items:** The complete fork choice specification changes need to be finalized in a PR, and there's an outstanding clarification needed about whether "locally available" implies "timely" availability.

## Participants

- fradamt
- m.kalinin
- nc1234
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-19T03:42:52.536000+00:00] m.kalinin: Summarizing:
* `payload_present` is FC enforced and the next slot proposer and attesters must adhere to it
* `blob_data_available` is a hint for the next slot proposer, attesters of the next slot doesn't pay attention to that flag and attest according to their own view on DA:
    * `blob_data_available = False`, `proposer_is_data_available = True` -> proposer can ignore `blob_data_available`
    * `blob_data_available = True`, `proposer_is_data_available = False` -> proposer is safe to build on top of the payload as likely the blobs are available

Is the above summary correct?
[2025-12-19T03:51:57.682000+00:00] nc1234: > payload_present is FC enforced and the next slot proposer and attesters must adhere to it
Are you talking about `payload_present` in `PayloadAttestationData` and thus `Store.ptc_vote`, or `LatestMessage.payload_present`?
[2025-12-19T03:53:28.731000+00:00] m.kalinin: I am thinking about a product of `blob_data_available` and `payload_present` from all `PayloadAttestationMessage` received
[2025-12-19T04:00:48.007000+00:00] m.kalinin: i.e. if more than a half of messages has `payload_present == True` then the proposer must build on top of the payload. While there is no such strict definition of how `blob_data_available` will be used, hypothetically the proposer may have a different threshold when the majority of `blob_data_available` flags diverge from its own view, i.e. build on top of payload only if `>3/4` of PTC members indicates data availability
[2025-12-19T04:10:48.939000+00:00] nc1234: > i.e. if more than a half of messages has payload_present == True then the proposer must build on top of the payload
I am curious about this as well. Looking from the spec, the `ptc_vote` only matters in the `get_head()`'s tie breaker in the event that two nodes having the same weight and same block root [here](https://github.com/ethereum/consensus-specs/blob/f2e0db481af9f49975b30972f2b275f2b26c916c/specs/gloas/fork-choice.md?plain=1#L427)
[2025-12-19T04:17:19.898000+00:00] m.kalinin: i need to carefully review the FC changes, don't have a strong intuition behind it yet
[2025-12-19T05:19:05.228000+00:00] terencechain: i am not sure if  you want to extend the payload if `blob_data_available = False`  even you have the payload. That means rest of the network may not see it . I think the attesters do look at `blob_data_availble` at least that's my understanding from Francesco's original document: https://hackmd.io/UX7Vhsv8RTy8I49Uxez3Ng which influenced current fork choice design. I very well could be wrong though
[2025-12-19T05:22:28.347000+00:00] m.kalinin: if attesters look at the `blob_data_available` then it means that PTC decides on the blob availability and not the attesters which I guess affects the DAS security assumption
[2025-12-19T07:59:47.687000+00:00] fradamt: Btw there are fork-choice changes that I posted here a long time ago that I still need to make in the main spec, so maybe wait until there is a PR with those to review the fork-choice specifically. Still, one thing that's going to stay true is that attesters only use the ptc's vote in order to decide whether to *enforce* that a payload is extended by the proposer, *as long as the payload is locally available*. Meaning, the attesters will always prioritize their own availability view over the ptc, so they are ultimately the source of truth.
[2025-12-19T08:04:16.243000+00:00] nc1234: > Btw there are fork-choice changes that I posted here a long time ago that I still need to make in the main spec
Do you mind forwarding those changes?
[2025-12-19T08:10:48.986000+00:00] nc1234: So if an attester:
- has payload locally available -> always enforce this view and disregard ptc votes
- has no payload locally available -> vote according to ptc votes the attester receives
Am I understanding this correctly?
[2025-12-19T10:23:35.712000+00:00] potuz: This is my reading as well. <@363800010518822915> I think with regards to DA it's just like <@425572898787426305> is saying: next attesters enforce it and the PTC is just hinting the proposer. 

The *only* place where I think leniency may be better is in the situation that the PTC votes for the payload not being timely but the proposer decides to build on top of it anyway. I think attesters that had their Payload validated (at time of attest, probably not even timely, that is that can sync the current CL block) should attest to this block. 

This favors chain liveness. The one danger I see from this is builder centralization if they start colluding with next proposers to distribute late their payload, but 1) this is something they can do today anyway and we aren't seeing this and 2) the next proposer can always reorg the Payload for free if they wish to
[2025-12-19T10:26:16.197000+00:00] m.kalinin: *locally available* doesn't imply timely, right?
```

</details>
