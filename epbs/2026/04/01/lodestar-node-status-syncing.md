# Devnet finalization and Lodestar checkpoint sync issues

**Channel:** epbs | **Date:** 2026-04-01

## Summary

The devnet has successfully finalized, but there are issues with Lodestar's checkpoint sync functionality when syncing from Prysm nodes. Lodestar is encountering a "getStateV2 failed with status 500: no finality known" error when attempting to fetch the checkpoint state from the checkpoint sync endpoint, which barnabasbusa initially attributed to a potential checkpointz bug.

Further investigation revealed that Lodestar is also unable to perform forward sync from a finalized checkpoint, with logs showing "Block not found for id 'head'" errors when trying to get block headers. This suggests a broader issue with Lodestar's sync process beyond just the initial checkpoint fetch.

nflaig has taken ownership of investigating the Lodestar sync issues, while barnabasbusa indicated they would look into the checkpointz component. The root cause and resolution remain pending investigation.

## Participants

- barnabasbusa
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-01T10:08:19.710000+00:00] barnabasbusa: we just finalized the devnet
[2026-04-01T10:11:54.422000+00:00] barnabasbusa: Lodestar doesn't like checkpoint syncing off of prysm ```
Apr-01 10:11:19.882[]                 info: Fetching checkpoint state checkpointSyncUrl=https://checkpoint-sync.epbs-devnet-1.ethpandaops.io
 ✖ Error: Unable to fetch weak subjectivity state: getStateV2 failed with status 500: no finality known
    at fetchWeakSubjectivityState (file:///usr/app/packages/cli/src/networks/index.ts:211:11)
    at processTicksAndRejections (node:internal/process/task_queues:104:5)
    at fetchWSStateFromBeaconApi (file:///usr/app/packages/cli/src/cmds/beacon/initBeaconState.ts:399:49)
    at initBeaconState (file:///usr/app/packages/cli/src/cmds/beacon/initBeaconState.ts:214:24)
    at Object.beaconHandler [as handler] (file:///usr/app/packages/cli/src/cmds/beacon/handler.ts:75:54)
```
[2026-04-01T10:12:52.157000+00:00] barnabasbusa: seems to be a checkpointz bug, gonna look into it
[2026-04-01T10:18:29.299000+00:00] barnabasbusa: <@586161934425128960>`Apr-01 10:17:49.511[rest]             warn: Req req-5l getBlockHeader failed reason=Block not found for id 'head'`  looks like lodestar is still unable to forward sync from a finalized checkpoint
[2026-04-01T11:24:28.815000+00:00] nflaig: looking into that
```

</details>
