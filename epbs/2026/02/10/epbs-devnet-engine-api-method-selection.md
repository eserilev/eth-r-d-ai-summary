# EPBS devnet-0 engine API method selection

**Channel:** epbs | **Date:** 2026-02-10

## Summary

tomi0x asked which Engine API method should be used for the EPBS (Enshrined Proposer-Builder Separation) devnet-0 implementation, specifically whether the Consensus Layer should use `engine_getPayloadV5` or `engine_getPayloadV6`.

terencechain responded that `engine_getPayloadV5` should be used, explaining that the development is "no where close to adding BAL or slot," indicating that the features associated with V6 (likely related to BAL and slot functionality) are not ready for implementation in the initial devnet.

## Participants

- terencechain
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-10T17:52:12.321000+00:00] tomi0x: For an epbs devnet-0, which engineApi method should the CL be using `engine_getPayloadV5` or `engine_getPayloadV6`?
[2026-02-10T18:32:29.763000+00:00] terencechain: `engine_getPayloadV5` please.... 
we are no where close to adding BAL or slot
```

</details>
