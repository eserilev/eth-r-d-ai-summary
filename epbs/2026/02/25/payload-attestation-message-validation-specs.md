# Payload attestation message validation checks in specs

**Channel:** epbs | **Date:** 2026-02-25

## Summary

The discussion centers on validation checks in the `on_payload_attestation_message` function, specifically whether a slot validation check should be moved to the p2p IGNORE conditions rather than being processed later. Terencechain questioned why payload attestation messages that fail the `data.slot != state.slot` check are gossiped at all, suggesting it would be more efficient to ignore them earlier in the p2p layer.

Potuz agreed that adding this check to the p2p IGNORE conditions would be beneficial and cause no harm, allowing clients to reject invalid messages earlier. However, he emphasized that the validation check should remain in the spec function as well, since it helps catch mistakes in tests and provides clarity. The consensus was that ignoring invalid attestations earlier in the p2p layer is a net positive improvement.

Terencechain clarified that he wasn't advocating for removing the checks from the spec entirely, but noted that client implementations might simplify their logic by relying on p2p filtering. He provided an example of how Prysm could potentially implement a streamlined version of `on_payload_attestation_message` that focuses only on updating the vote tracking, treating the specific validation approach as an implementation detail while avoiding duplicate checks.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-25T15:55:32.772000+00:00] terencechain: In `on_payload_attestation_message`, there's this check:
```
    state = store.block_states[data.beacon_block_root]
    # PTC votes can only change the vote for their assigned beacon block, return early otherwise
    if data.slot != state.slot:
        return
```
I dont understand why isn't this check part of IGNORE for p2p spec. What would be the benefit of gossip this kind pf payload attestation message first place
[2026-02-25T16:54:43.779000+00:00] potuz: It can be added I see no harm in it
[2026-02-25T16:55:07.410000+00:00] potuz: we would still need the check in processing though
[2026-02-25T17:02:40.754000+00:00] terencechain: im not sure. For block's payload attestations, we already have:
```
    assert data.beacon_block_root == state.latest_block_header.parent_root
    assert data.slot + 1 == state.slot
```
which to me looks logically equivalent but im the stupid one here
[2026-02-25T17:09:44.806000+00:00] potuz: for attestations over the wire we do not have these, which is why we add them. I agree with you that we can implement clients without this check if we guarantee that we are ignoring them, but I think the spec should still have these checks because it helps catch up mistakes in tests
[2026-02-25T17:10:14.083000+00:00] potuz: It's fine by me if you want to stop these attestations from reaching forkchoice altogether earlier, it's just performing the check earlier,
[2026-02-25T17:10:25.626000+00:00] potuz: but I think we should not remove the check from that spec function
[2026-02-25T17:12:54.468000+00:00] terencechain: Sure. I think
- it makes sense to add them to p2p and clients have them in p2p. Ignore earlier is better. It's net positive improvement
- Not calling to remove them in spec, but i think clients may not have them. For example, in prysm, I argue all we need is the following for `on_payload_attestation_message` but that's really a client implementation detail. I personaly dont like dup checks
```
    ptc_index = ptc.index(ptc_message.validator_index)
    payload_timeliness_vote = store.payload_timeliness_vote[data.beacon_block_root]
    payload_timeliness_vote[ptc_index] = data.payload_present
    payload_data_availability_vote = store.payload_data_availability_vote[data.beacon_block_root]
    payload_data_availability_vote[ptc_index] = data.blob_data_available
```
```

</details>
