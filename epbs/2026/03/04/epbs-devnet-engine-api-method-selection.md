# EPBS devnet-0 payload envelope fetching implementation

**Channel:** epbs | **Date:** 2026-03-04

## Summary

0xunclebill announced updates to the EPBS devnet-0 implementation, specifically adding the ability to fetch individual payload envelopes via RPC when importing blocks with unknown parent payloads. This addresses scenarios where nodes need to retrieve missing payload data that wasn't received through the normal gossip network.

The implementation has been tested locally against multiple Ethereum client implementations (Lodestar, Prysm, and Lighthouse) in configurations where payload envelope gossip was intentionally disabled, and the RPC-based fetching mechanism appears to be functioning correctly as a fallback method.

## Participants

- 0xunclebill

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T07:15:00.526000+00:00] 0xunclebill: pushed some last minutes changes to our epbs-devnet-0 branch. we can now fetch single envelopes over rpc when trying to import a block whose parent payload is unknown. ive tested this locally against lodestar, prysm + LH nodes that purposely dont gossip payload envelopes and it seems to be working.
```

</details>
