# ExecutionPayloadEnvelope Slot Field Changes

---

## 2026-04-16 (epbs)

The discussion centers on changes to EIP-7843 regarding the `ExecutionPayloadEnvelope` structure. jtraglia has updated the PR to remove the `slot` field from `ExecutionPayloadEnvelope` since the `ExecutionPayload` now contains a `slot_number` field, making the envelope's slot field redundant.

While this represents a consensus breaking change, jtraglia noted that now is the appropriate time to implement such modifications. terencechain confirmed the approach by asking for clarification about removing the slot field from `ExecutionPayloadEnvelope`, and jtraglia confirmed that this is indeed what was done in the updated PR.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-16T19:41:48.870000+00:00] jtraglia: I've made some more changes to the eip7843 PR. Now that `ExecutionPayload` contains the `slot_number` field, we can remove the `slot` field from `ExecutionPayloadEnvelope`. Obviously a consensus breaking change, but now is the time to make the change.
[2026-04-16T19:57:11.624000+00:00] terencechain: we can remove `slot` from the `ExecutionPayloadEnvelope` right?
[2026-04-16T20:21:08.997000+00:00] jtraglia: Yeah I think so. That's what I did.
```

</details>

---

## 2026-04-23 (epbs)

This discussion consists of a single message from jtraglia proposing to add `parent_beacon_block_root` to the `ExecutionPayloadEnvelope` structure. 

No further discussion, consensus, or implementation details were provided in the available messages, leaving the proposal's rationale, impact, and next steps unclear.

**Participants:** jtraglia

<details>
<summary>Raw messages</summary>

```
[2026-04-23T18:44:45.032000+00:00] jtraglia: Adding `parent_beacon_block_root` to `ExecutionPayloadEnvelope`
```

</details>

