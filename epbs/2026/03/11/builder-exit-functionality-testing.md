# Builder exit functionality and testing

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion centers on testing builder exit functionality for 0x03 builders using the `/eth/v1/beacon/pool/voluntary_exits` API endpoint. barnabasbusa encountered issues where none of the major Ethereum clients (Lighthouse, Lodestar, Prysm) properly support the builder flag on this endpoint yet - Lighthouse doesn't recognize the builder index flag, Lodestar's API validation lacks builder-aware code and only handles validator pubkeys, and Prysm treats builder indices as regular validator indices causing out-of-bounds errors. While nflaig confirmed clients can process builder exits when received via gossip, the API submission path remains broken.

Testing revealed that while 0x00-0x03 deposits and withdrawals work without breaking the chain, builder exits cause significant issues. When exits are processed, Prysm encounters "invalid forkchoice state" errors from the execution engine and starts missing blocks, while Lodestar experiences fork choice and state regeneration problems. potuz noted these issues were expected since the functionality hasn't been fully implemented yet, mentioning that fixes are needed in both the geth branch to accept forkchoice updates to older heads and in clients to stop voting for empty blocks.

The current status shows deposits and withdrawals working, but exits and builder index recycling remain non-functional. Open issues include updating all client API validation layers to support builder-aware exit processing and resolving the execution engine integration problems that cause chain instability when builder exits are included in blocks.

## Participants

- barnabasbusa
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T15:02:56.823000+00:00] barnabasbusa: what beacon api am I supposed to hit to exit a 0x03 builder?
[2026-03-11T15:05:21.561000+00:00] nflaig: `POST /eth/v1/beacon/pool/voluntary_exits`
[2026-03-11T15:07:20.600000+00:00] barnabasbusa: yeah thats what I thought
[2026-03-11T15:07:31.828000+00:00] barnabasbusa: any of the clients supposed to support the builder flag already on this endpoint?
[2026-03-11T15:07:47.495000+00:00] barnabasbusa: keep getting `VOLUNTARY_EXIT_ERROR_INVALID_SIGNATURE`
[2026-03-11T15:10:55.641000+00:00] barnabasbusa: Lighthouse: ValidatorUnknown - doesn't recognize the builder index flag
Lodestar: Gets to signature verification but the API validation layer (validateApiVoluntaryExit) doesn't have builder-aware code - it uses getVoluntaryExitSignatureSet which only looks up validator pubkeys, not builder pubkeys
Prysm: index out of bounds - treats it as a regular validator index
[2026-03-11T15:12:12.860000+00:00] nflaig: can confirm, this code path is not updated yet, we can process them though
[2026-03-11T15:12:34.328000+00:00] barnabasbusa: You can process, but I can't submit 😂
[2026-03-11T15:12:56.876000+00:00] nflaig: you can gossip them yourself 🤷‍♂️
[2026-03-11T15:13:05.685000+00:00] barnabasbusa: local kurtosis tests confirm that 0x00,0x01,0x02,0x03 deposits don't break the chain
[2026-03-11T15:13:35.250000+00:00] potuz: deposits, withdrawals most probably so exits should as well 🙂
[2026-03-11T15:13:46.194000+00:00] barnabasbusa: yup, exits don't break either
[2026-03-11T15:13:48.562000+00:00] potuz: do not send them even if you can 🙂
[2026-03-11T15:13:50.884000+00:00] barnabasbusa: been trying to exit the builder
[2026-03-11T15:14:32.280000+00:00] barnabasbusa: https://klipy.com/gifs/soft-kobe-bryant
[2026-03-11T15:17:39.469000+00:00] barnabasbusa: Deposits ✅ 
Exits ❌ 
Withdrawals ✅ 
Exit of 0x03 and testing recycling of builder index ❌
[2026-03-11T15:18:49.075000+00:00] potuz: you're too hopeful, need to try withdrawals with empty blocks
[2026-03-11T15:18:53.260000+00:00] potuz: both missing and empty
[2026-03-11T15:19:01.855000+00:00] potuz: not related to builders
[2026-03-11T15:20:04.313000+00:00] barnabasbusa: actually, the exits just got processed
[2026-03-11T15:20:05.626000+00:00] barnabasbusa: `[2026-03-11 15:19:52.54] ERROR blockchain: received an undefined execution engine error error=invalid forkchoice state`
[2026-03-11T15:20:10.825000+00:00] barnabasbusa: I spoke too soon that it was working
[2026-03-11T15:20:20.319000+00:00] barnabasbusa: prysm right away started missing blocks when it had to include an exit
[2026-03-11T15:20:55.771000+00:00] barnabasbusa: ```
Mar-11 15:20:36.128[rest]            error: Req req-2p getStateFinalityCheckpoints error - REGEN_ERROR_NO_SEED_STATE
Error: REGEN_ERROR_NO_SEED_STATE
    at StateRegenerator.getState (file:///usr/app/packages/beacon-node/src/chain/regen/regen.ts:210:13)
    at JobItemQueue.jobQueueProcessor [as itemProcessor] (file:///usr/app/packages/beacon-node/src/chain/regen/queued.ts:320:35)
    at Timeout.runJob [as _onTimeout] (file:///usr/app/packages/beacon-node/src/util/queue/itemQueue.ts:149:33)
    at listOnTimeout (node:internal/timers:605:17)
    at processTimers (node:internal/timers:541:7)
```
[2026-03-11T15:21:01.775000+00:00] barnabasbusa: lodestar also having issues
[2026-03-11T15:21:27.517000+00:00] nflaig: thats only on the api, shouldn't be critical
[2026-03-11T15:21:34.849000+00:00] potuz: yeah that I was expecting, I don't think we even checked any of it yet. The invalid forkchoice state comes from the engine and Prysm will continue having this until we update the geth branch to accept an FCU to an older head and until clients stop voting for empty
[2026-03-11T15:22:06.991000+00:00] barnabasbusa: ~~looks like we are recovering tho~~
[2026-03-11T15:22:58.862000+00:00] barnabasbusa: lodestar: 
```
Mar-11 15:22:45.043[]                error: Error signing SyncCommitteeContribution slot=604, index=3, validatorIndex=143 - produceSyncCommitteeContribution failed with status 404: Block not in forkChoice, beaconBlockRoot=0xa74262ce35ee6f693e9293648fd7d7f7c9f4f2b980366b7415029aa0fab27a8d
Error: produceSyncCommitteeContribution failed with status 404: Block not in forkChoice, beaconBlockRoot=0xa74262ce35ee6f693e9293648fd7d7f7c9f4f2b980366b7415029aa0fab27a8d
    at ApiResponse.error (file:///usr/app/packages/api/src/utils/client/response.ts:165:12)
    at ApiResponse.assertOk (file:///usr/app/packages/api/src/utils/client/response.ts:156:18)
    at ApiResponse.value (file:///usr/app/packages/api/src/utils/client/response.ts:105:10)
    at file:///usr/app/packages/validator/src/services/syncCommittee.ts:221:92
    at Array.map (<anonymous>)
    at SyncCommitteeService.produceAndPublishAggregates (file:///usr/app/packages/validator/src/services/syncCommittee.ts:215:14)
    at processTicksAndRejections (node:internal/process/task_queues:104:5)
    at file:///usr/app/packages/validator/src/services/syncCommittee.ts:105:11
    at async Promise.all (index 3)
    at runSyncCommitteeTasks (file:///usr/app/packages/validator/src/services/syncCommittee.ts:100:7)
```
[2026-03-11T15:23:54.127000+00:00] barnabasbusa: Yeah exits seem to mess up lodestar and prysm completely
[2026-03-11T15:24:00.988000+00:00] barnabasbusa: lighthouse trying to still producing blocks tho
```

</details>
