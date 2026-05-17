# Function naming for process_execution_payload

**Channel:** epbs | **Date:** 2025-12-03

## Summary

The discussion centers around naming conventions for the `process_execution_payload()` function that is being repurposed as a standalone function outside of block processing. nc1234 raised concerns about potential namespace conflicts and suggested renaming the new function in "gloas" to `process_execution_payload_envelope` for better clarity and to avoid conflicts with existing code.

stefanbratanov responded favorably to the current naming approach, arguing that it maintains alignment with the existing gossip topic naming convention of `execution_payload`. However, the discussion appears to remain open without a clear consensus reached on whether to proceed with the rename or maintain the current naming structure.

## Participants

- nc1234
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-03T03:46:06.427000+00:00] stefanbratanov: good point, I don't think it's too bad to change them now
[2025-12-03T05:58:00.905000+00:00] nc1234: How is everyone feeling about the name `process_execution_payload()` being re-purposed as a standalone function outside of block processing function? I think the new `process_execution_payload` in gloas should be named `process_execution_payload_envelope` to avoid namespace conflict + extra clarity in our codebase
[2025-12-03T08:37:44.390000+00:00] stefanbratanov: I like the current naming since it aligns with the gossip topic `execution_payload`
```

</details>
