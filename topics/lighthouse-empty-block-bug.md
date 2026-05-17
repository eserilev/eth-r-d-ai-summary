# Lighthouse Empty Block Bug

---

## 2026-05-01 (epbs)

A bug was identified in Lighthouse where the execution layer (EL) fails to propose when the first block in a sequence is empty. This appears to be a specific edge case that causes the proposal mechanism to malfunction under these particular conditions.

The discussion consists of a single report from potuz identifying this issue, but no further details about potential fixes, workarounds, or timeline for resolution were provided in the conversation.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-01T08:06:26.051000+00:00] potuz: yeah this was a bug that was hit by Lighthouse when the first block was empty, the EL fails to propose in that case
```

</details>

