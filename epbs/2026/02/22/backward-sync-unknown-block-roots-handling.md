# Backward sync and unknown block roots handling

**Channel:** epbs | **Date:** 2026-02-22

## Summary

The discussion centers on handling unknown block roots during backward synchronization in Ethereum clients. Dapplion initially mentions a scenario involving backward sync when encountering an unknown root, prompting potuz to question why state transitions would be necessary during backward sync.

Potuz clarifies the scenario: when a client receives a block with an unknown parent, it needs to request blocks backward to fill the gap in the chain. He suggests this isn't a significant technical problem since the block contains both a parent block root and parent block hash, allowing the client to request whichever piece of information it lacks to continue the synchronization process.

The conversation appears to resolve around a straightforward solution for handling missing parent blocks during sync, though no explicit consensus or action items are documented in these messages.

## Participants

- dapplion
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-22T01:17:22.769000+00:00] dapplion: No backwards sync, when you get an unknown root
[2026-02-22T01:18:54.071000+00:00] potuz: Why do you need to do a state transition on backward sync?
[2026-02-22T01:19:43.694000+00:00] potuz: Oh you mean you get a block with an unknown parent and you need to start requesting back to fill the gap?
[2026-02-22T01:23:27.730000+00:00] potuz: If that's the case then i don't see what's the big problem: you got a block that refers both a parent blockroot and a parent block hash. You request any of those two that you do not know about.
```

</details>
