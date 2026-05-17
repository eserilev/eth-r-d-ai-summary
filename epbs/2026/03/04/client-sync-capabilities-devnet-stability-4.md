# Network Status and Client Health Analysis

**Channel:** epbs | **Date:** 2026-03-04

## Summary

barnabasbusa reported client health issues across the Ethereum network. Nimbus is experiencing crash looping behavior, while Teku is rejecting an invalid block at slot 455 (root `0x11402cbcb0e196c678c97de23eb5ea884636b52acca812d840febe8eaa15d270`) due to a failed state transition. Teku is also encountering an `UnsupportedOperationException` related to unimplemented lookup functionality for finalized execution payload envelopes.

In contrast, Lighthouse (lh) and Lodestar clients appear to be operating normally without reported issues. The status update suggests a mixed network health situation with some clients experiencing technical difficulties while others remain stable.

## Participants

- barnabasbusa

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T16:13:39.667000+00:00] barnabasbusa: nimbus is crash looping, 
teku is rejecting invalid block due to failed state transition `Rejecting invalid block at slot 455 with root 0x11402cbcb0e196c678c97de23eb5ea884636b52acca812d840febe8eaa15d270 because FAILED_STATE_TRANSITION` and also `UnsupportedOperationException: Lookup of finalized execution payload envelopes is not implemented yet`
[2026-03-04T16:13:50.846000+00:00] barnabasbusa: lh and lodestar seems healthy
```

</details>
