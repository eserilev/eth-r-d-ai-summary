# Implementation Complexity and Caching Architecture Changes

**Channel:** epbs | **Date:** 2026-04-09

## Summary

Potuz discusses the implementation complexity of a proposed architectural change, noting that while it would be more complex than their current approach, the fundamental difference is relatively small. Currently, their system has clear logic for when to perform state transitions and preemptively cache epoch transitions, and the new approach would require moving post-payload operations to post-block timing, which would fundamentally change their range syncing implementation.

The core technical change involves reordering epoch processing and the last payload processing, plus removing the state_root from the envelope. Despite the operational complexity this would introduce for their team, Potuz acknowledges that the actual content of the change is quite limited in scope.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-09T15:36:44.264000+00:00] potuz: yeah that's what I am not sure about, I think implementing this approach would be actually more complex than the current approach for us.  Right now it is simpler to reason about when to perform a state transition, and when to preemtively cache epoch transition. This probably is not that big of a change in this regard, things we do post-payload would be moved to post-block, but range syncing would be fundamentally different with this approach for us at least
[2026-04-09T15:37:11.035000+00:00] potuz: technically the only difference is in a single change of ordering between epoch processing and the last payload processing
[2026-04-09T15:37:31.756000+00:00] potuz: that's the whole content of the change (besides having to remove the state_root from the envelope)
```

</details>
