# Forkchoice Optimistic Sync LVH Removal Issues

**Channel:** epbs | **Date:** 2026-02-05

## Summary

Potuz identified a technical issue with forkchoice implementations during optimistic sync that affects how nodes are removed when receiving an INVALID response with a non-trivial Last Valid Hash (LVH) from the Execution Layer (EL). The problem arises when catching up and removing nodes up to the LVH point in the forkchoice tree.

The implementation challenge is that instead of simply removing the full node containing the payload (as was done previously), the system now needs to traverse and remove all children nodes (both empty and full) from the full node while preserving the empty node that contained the payload commitment. Importantly, descendants of this preserved empty node should not be removed during this cleanup process.

Potuz mentioned wanting to document this finding in annotated docs and tagged another team member, suggesting this is an implementation detail that needs to be shared with the broader development community working on forkchoice algorithms.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-05T12:59:44.047000+00:00] potuz: I don't know where to share this, perhaps in my annotated docs, but I wanted to share a little perk that became a problem in  forkchoice implementations. When doing optimistic sync, catching up and removing up to the LVH because of receiving an INVALID with a non-trivial LVH from the EL, removing nodes in forkchoice may be a problem. Previously it would remove the full node containing the payload, now it needs to traverse removing all children (both empty and full) from the full node, but leave the empty node that contained the payload commitment and should **not** remove descendants of this node.
[2026-02-05T13:00:10.539000+00:00] potuz: cc <@602753420033785856>
```

</details>
