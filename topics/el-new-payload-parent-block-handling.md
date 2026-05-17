# EL New Payload Parent Block Handling

---

## 2026-04-11 (epbs)

Potuz raised a question about Execution Layer (EL) behavior when receiving a new payload without having the parent block available. They asked whether the EL would return a "syncing" status in this scenario and whether the EL would automatically request the missing parent block on its own.

This appears to be an open question about the EL's handling of payload validation dependencies, with no responses or resolution provided in the discussion.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-11T22:03:43.582000+00:00] potuz: What happens in the EL if we send a new payloads for which they don't have the parent, do they return syncing? Do they request the parent on their own?
```

</details>

