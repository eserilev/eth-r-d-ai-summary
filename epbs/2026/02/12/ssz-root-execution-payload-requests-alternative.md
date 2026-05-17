# SSZ root of ExecutionPayload and ExecutionRequests alternative

**Channel:** epbs | **Date:** 2026-02-12

## Summary

The discussion explores an alternative approach to handling SSZ roots for `ExecutionPayload` and `ExecutionRequests`. ethdreamer suggests that the system could function by committing to just the SSZ root of these two components, either by including both roots separately or by combining both objects into a single structure and committing to that combined object's root.

However, ethdreamer quickly identifies a potential security issue with this approach, noting that the `state_root` would need to be removed from the proposal, as its inclusion would allow validators to easily lie about the state root while still providing valid SSZ roots for the execution payload and requests. This constraint appears to be a significant limitation of the proposed alternative approach.

## Participants

- ethdreamer

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-12T21:25:22.055000+00:00] ethdreamer: technically, wouldn't everything work if we just had the ssz root of the `ExecutionPayload` & `ExecutionRequests`? We could either have both roots or put both in the same object and commit to that object's root.
[2026-02-12T21:30:22.153000+00:00] ethdreamer: oh i suppose we'd have to remove the `state_root` or they could lie about that easily
```

</details>
