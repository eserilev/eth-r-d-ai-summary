# Devnet-1 Launch and Network Status

---

## 2026-03-31 (epbs)

The team launched epbs-devnet-1 with genesis occurring successfully at 12:04 UTC, followed by the "gloas" event 25 minutes later. However, the network experienced issues during and after gloas, with validator clients struggling to submit aggregate selection proofs to beacon nodes, encountering "DeadlineExceeded" errors around slot 225.

Lodestar appears to be maintaining network stability while other client implementations face difficulties. Specific issues were noted with validator clients experiencing timeout errors, and there were mentions of problems with validator monitor processes during end-of-epoch operations. The team identified that these errors were not occurring on the "kt" implementation, suggesting client-specific issues rather than protocol-level problems.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

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

