# Fork Choice Spec Pre-Gloas Compatibility Issue

**Channel:** epbs | **Date:** 2026-04-10

## Summary

nflaig identified a compatibility issue in the fork choice specification where code assumes the existence of `signed_execution_payload_bid.message` in block bodies, which breaks when the parent block is from before the Gloas fork. The problematic code directly accesses `block.body.signed_execution_payload_bid.message` without checking if the parent block predates Gloas, which appears to be the first fork where this field is relevant.

potuz confirmed this is a known issue and assumes all clients handle this case properly in their implementations. However, he noted that the specification itself is fork-specific, and incorporating fork awareness into the helper functions would make the spec uglier. The discussion leaves open how this should be properly addressed in the specification while maintaining clean code structure.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-10T10:45:44.927000+00:00] nflaig: in the fork choice spec we have
```python
 parent_block = store.blocks[block.parent_root]
 bid = block.body.signed_execution_payload_bid.message
```
this breaks if parent is pre-gloas, does fork choice just not care about this? it seems like gloas is the first time this is relevant
[2026-04-10T12:25:58.711000+00:00] potuz: yeah this was noticed before and I assume all clients case this, the problem is that the spec is fork-specific, including fork awareness in the helpers is ugly.
```

</details>
