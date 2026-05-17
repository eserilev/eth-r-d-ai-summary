# Assertoor builder deposit spam testing issues

**Channel:** epbs | **Date:** 2026-05-10

## Summary

The discussion centers on troubleshooting issues with Assertoor's builder deposit spam testing functionality. Initially, terencechain encountered an error where the `generate_batch_deposits` task was not recognized. jtraglia identified that support for this feature was only merged into master 3 days prior and suggested force-pulling new Docker images from `ethpandaops/assertoor:master` to resolve the issue.

After updating the images, terencechain encountered a configuration validation error requiring `limitPerSlot`, `limitTotal`, or `indexCount` to be set, which was resolved by setting the value to 1000. However, when attempting to test with 8192 deposits, the system failed with an "oversized data" error due to a transaction size of 1,442,245 bytes exceeding the 131,072 byte limit. jtraglia noted that testing 8192 deposits requires a 200m gas limit rather than the apparent 150m limit being used.

The conversation successfully resolved the initial setup issues, but the large batch testing remains limited by transaction size constraints that require specific gas limit configurations to handle the increased data payload.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-10T15:08:39.740000+00:00] terencechain: ```
2026-05-10 08:05:21 PDT
time="2026-05-10T15:05:21Z" level=error msg="could not schedule startup test execution for builder-deposit-spam (Spam chain with batched 0x03 builder deposits): failed initializing test run #1 'Spam chain with batched 0x03 builder deposits': unknown task name: generate_batch_deposits"
```
[2026-05-10T15:15:20.758000+00:00] jtraglia: The `ethpandaops/assertoor:master` image should have support for that. Maybe you need to force pull new images?
[2026-05-10T15:17:42.377000+00:00] jtraglia: Support for this was only merged into master 3 days ago btw.
[2026-05-10T16:36:24.829000+00:00] terencechain: nice! that tells you how much i know about docker 🙂 
new failure but i can figure it out...
```
[ERR] config validation failed: either limitPerSlot or limitTotal or indexCount must be set
```
[2026-05-10T16:51:54.332000+00:00] terencechain: 1000 works
[2026-05-10T16:52:02.773000+00:00] terencechain: i tried to go 8192 deposits and got this from assertor: `failed sending batch tx: oversized data: transaction size 1442245, limit 131072`
[2026-05-10T16:52:51.913000+00:00] jtraglia: Nice. Are you using 150m gas limit? Needs 200m for 8192.
```

</details>
