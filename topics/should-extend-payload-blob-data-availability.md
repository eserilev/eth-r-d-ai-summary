# should_extend_payload method blob data availability clarification

---

## 2026-04-13 (epbs)

bharath.vedartham raised a concern about the `should_extend_payload` method's documentation and implementation. The documentation states that if blob data is not available, the payload should not be extended, but the actual Python code appears to allow extension in certain cases even when blob data is unavailable. Specifically, the method returns true if any of several conditions are met, including cases where the proposer root is empty, has a different parent, or meets other criteria that don't explicitly check for blob data availability.

potuz responded by clarifying that if blob data is not available, the payload cannot be synced in the first place, so the `should_extend_payload` function would not even be reached in such scenarios. This suggests that the apparent discrepancy between documentation and code may not be a practical issue since the function operates under the assumption that blob data availability has already been established during the sync process.

The discussion appears to resolve the initial concern, though no explicit confirmation was provided that the documentation should be updated to reflect this implementation detail.

**Participants:** bharath.vedartham, potuz

<details>
<summary>Raw messages</summary>

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

