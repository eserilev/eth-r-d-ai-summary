# Optimistic Sync and ExecutionPayloadEnvelope Requirements

**Channel:** epbs | **Date:** 2026-04-10

## Summary

The discussion focused on the requirements for `execution_requests` within the `ExecutionPayloadEnvelope` structure. Initially, there was consideration that `execution_requests` might no longer be necessary in the envelope if `engine_newPayload` could return them instead. However, upon further reflection, it was determined that including `execution_requests` in the `ExecutionPayloadEnvelope` remains necessary specifically to support optimistic sync functionality.

This represents a clarification of the technical requirements where the optimistic sync process creates a dependency that prevents the removal of `execution_requests` from the envelope structure, even though alternative approaches like having `engine_newPayload` return them might work for other use cases.

## Participants

- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-10T12:17:22.699000+00:00] nflaig: >  I think we don't even need execution_requests in the ExecutionPayloadEnvelope anymore but that would require engine_newPayload to return them instead
ah realized this is necessary for optimistic sync
```

</details>
