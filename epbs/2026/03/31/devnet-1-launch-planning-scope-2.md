# Devnet-1 Genesis Launch and Network Monitoring

**Channel:** epbs | **Date:** 2026-03-31

## Summary

The team launched Devnet-1 genesis successfully at the scheduled time, with network monitoring available at the provided dashboard link. However, the "gloas" event (likely related to network activation) that occurred 25 minutes after genesis did not proceed smoothly, with validator clients experiencing timeout errors when submitting aggregate selection proofs to beacon nodes.

The network appeared to have issues with validator operations, as evidenced by "DeadlineExceeded" RPC errors at slot 225. Barnabasbusa noted that Lodestar appears to be carrying the network operations while other clients struggle, and mentioned that certain validator client implementations (referencing "kt") have not encountered these specific errors. An additional error was reported regarding the validator monitor's "onceEveryEndOfEpoch" function, suggesting ongoing validator-related issues that may require investigation.

## Participants

- barnabasbusa

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-31T11:50:03.836000+00:00] barnabasbusa: https://dora.epbs-devnet-1.ethpandaops.io
[2026-03-31T11:50:14.089000+00:00] barnabasbusa: T-10mins
[2026-03-31T12:04:07.511000+00:00] barnabasbusa: we had genesis
[2026-03-31T12:04:17.398000+00:00] barnabasbusa: gloas is in 25mins
[2026-03-31T12:45:06.443000+00:00] barnabasbusa: looks like gloas didn't went too smoothly
[2026-03-31T12:45:47.572000+00:00] barnabasbusa: `[2026-03-31 12:45:12.01] ERROR client: Could not submit aggregate selection proof to beacon node error=rpc error: code = DeadlineExceeded desc = context deadline exceeded slot=225`
[2026-03-31T12:46:44.008000+00:00] barnabasbusa: looks like lodestar is carrying the network now
[2026-03-31T12:51:34.304000+00:00] barnabasbusa: <@755590043632140352> <@363800010518822915> I have not seen this error on kt at all. Validator client is struggling
[2026-03-31T13:00:35.830000+00:00] barnabasbusa: <@586161934425128960> error on validator monitor onceEveryEndOfEpoch
```

</details>
