# Engine commitments and payload verification

**Channel:** epbs | **Date:** 2026-01-27

## Summary

In this brief discussion, potuz reconsidered their previous position on engine commitments and payload verification. They noted that while their earlier suggestion might be technically feasible, it creates a practical problem: the engine requires commitments to verify payloads, and excluding these commitments from the envelope would force nodes to obtain a DCS (likely Data Commitment Scheme) just to perform payload verification.

This appears to be an argument for including commitments in the envelope to avoid the additional overhead and complexity of requiring separate DCS retrieval for basic payload verification operations.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T15:01:32.874000+00:00] potuz: I take it back as well perhaps, while it's doable, the engine needs the commitments to verify the payload, and if we don't include them in the envelope then this forces the node to get a DCS to verify the payload
```

</details>
