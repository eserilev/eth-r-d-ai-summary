# should_extend_payload method and blob data availability

**Channel:** epbs | **Date:** 2026-04-13

## Summary

bharath.vedartham raised a concern about the `should_extend_payload` method, noting that its description states "If the blob data is not available, we do not extend it" but the actual implementation doesn't seem to reflect this constraint. They pointed out that the method could potentially return true for extending a payload even when blob data is unavailable, particularly in cases where the proposer root beacon block extends on the parent block.

potuz responded by clarifying that if blob data is not available, the payload cannot be synced in the first place, which means the `should_extend_payload` function wouldn't even be reached in such scenarios. This explanation appears to resolve the apparent contradiction between the method's description and implementation, suggesting the constraint is enforced at an earlier stage in the process rather than within this specific function.

## Participants

- bharath.vedartham
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T17:07:28.680000+00:00] bharath.vedartham: I have a question, the description of `should_extend_payload` says `If the blob
data is not available, we do not extend it`. but that is not reflected in the method as below.:
```python
def should_extend_payload(store: Store, root: Root) -> bool:
    proposer_root = store.proposer_boost_root
    return (
        (is_payload_timely(store, root) and is_payload_data_available(store, root))
        or proposer_root == Root()
        or store.blocks[proposer_root].parent_root != root
        or is_parent_node_full(store, store.blocks[proposer_root])
    )
```
We could have a case where the proposer root beacon block extends on the parent block even if the blob data is not available and `should_extend_payload` will return true?
[2026-04-13T17:36:30.735000+00:00] potuz: If the blob data is not available then you can't even sync the payload.
[2026-04-13T17:36:54.010000+00:00] potuz: so this function even gets reached
```

</details>
