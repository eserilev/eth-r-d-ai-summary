# Finalized state handling and checkpoint states in Gloas

**Channel:** epbs | **Date:** 2026-04-04

## Summary

tuyen4818 raised concerns about finalized state handling in Gloas when dealing with skipped slot finalized checkpoints, noting that Lodestar may face similar issues when restarting nodes if using postState as the finalized state. They emphasized that the specification doesn't clearly address this case, requiring deeper analysis to clarify the behavior for future reference.

The discussion focused on three key technical questions: whether finalized/justified states are equivalent to `store.checkpoint_states[cp]` in the forkchoice spec (assumed true for pre-Gloas but unclear for Gloas), whether the current Phase 0 `store_target_checkpoint_state` computation remains correct for Gloas, and how to properly handle the base state selection. tuyen4818 pointed out that the current implementation always uses `store.block_states` for `base_state`, but argued that for Gloas attestations, it should also consider `store.payload_states` when processing to the target epoch.

A significant concern was raised about potential conflicts when updating `store.checkpoint_states[target]` based on either block states or payload states from attestation data, as one could override the other. tuyen4818 suggested that checkpoint states might need to be stored by both checkpoint and payload_status to avoid this issue. The discussion appears to be seeking input from other developers on these specification and implementation questions.

## Participants

- tuyen4818

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-04T08:31:40.187000+00:00] tuyen4818: yes I believe lodestar will also have the same issue when restarting the node with skipped slot finalized state, if we use postState as the finalized state
[2026-04-04T08:47:54.936000+00:00] tuyen4818: my concern is we cannot count on the spec for this case (the finalized state of skipped slot finalized checkpoint), it means that we need to dig into the spec a bit to make it clear for future reference

could you guys give your inputs:
- 1) is finalized/justified state the same to `store.checkpoint_states[cp]` in forkchoice spec? I assume it is for pre-gloas, need to clarify for gloas if it's not
- 2) is the `store.checkpoint_states` computation in phase0 correct for gloas?
```python
def store_target_checkpoint_state(store: Store, target: Checkpoint) -> None:
    # Store target checkpoint state if not yet seen
    if target not in store.checkpoint_states:
        base_state = copy(store.block_states[target.root])
        if base_state.slot < compute_start_slot_at_epoch(target.epoch):
            process_slots(base_state, compute_start_slot_at_epoch(target.epoch))
        store.checkpoint_states[target] = base_state
```

for me the `base_state` is always `store.block_states` which is not correct. Given a specific  gloas attestation (check `on_attestation()` phase0), `base_state` could be from `store.payload_states` too dialing up to target epoch, we need to update that?

- 3) but updating `store.checkpoint_states[target]` based on either `store.block_states` vs `store.payload_states` given an attestation data is also not correct because one would override the other. We need to store `checkpoint_state` by checkpoint + payload_status too?
```

</details>
