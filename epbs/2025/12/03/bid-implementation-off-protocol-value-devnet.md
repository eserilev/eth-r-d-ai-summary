# Execution payment bid mechanism discussion

**Channel:** epbs | **Date:** 2025-12-03

## Summary

bharath.vedartham raised a question about the execution payment bid mechanism, specifically whether `bid.execution_payment` is only used for off-protocol payments with no consensus-layer impact. They explored a scenario where both `bid.value` and `bid.execution_payment` are set in a `SignedExecutionPayloadBid` sent from builder to proposer via HTTP.

bharath.vedartham concluded that setting both values should have no protocol impact - the builder would pay `bid.value` through the trustless protocol mechanism and `bid.execution_payment` off-protocol, though the latter cannot be enforced at the protocol level. potuz confirmed this understanding, noting that builders can use this mechanism to signal additional off-protocol payments if desired.

## Participants

- bharath.vedartham
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-03T14:36:13.979000+00:00] bharath.vedartham: so if i m right, `bid.execution_payment` is only set for off-protocol payments purpose and there is no use of it in consensus? i m thinking of what would happen if `bid.value` and `bid.execution_payment` both are set to some value. if a builder sends a `SignedExecutionPayloadBid` to a proposer via a HTTP connection, they can set both values.
There should ideally be no impact to doing this. the builder would end paying `bid.value` in the trustless way and the `bid.execution_payment` off-protocol(although there is no way to enforce this in protocol)
[2025-12-03T18:44:09.975000+00:00] potuz: correct, the builder can use this mechanism to signal an extra payment off-protocol if so they desire
```

</details>
