# Client sync issues and debugging updates

**Channel:** epbs | **Date:** 2026-03-11

## Summary

The discussion centers around client synchronization issues affecting multiple Ethereum clients. Nimbus-geth-1 is experiencing sync problems with only 9.35% progress and failing to receive block data due to server errors, while Teku-geth-1 is encountering execution payload import errors with "Bid is not for the right parent block" exceptions. A key technical discrepancy has been identified where Lighthouse handles `latest_block_hash` differently from Prysm and Lodestar - using `bid.block_hash` (payload applied) versus `bid.parent_block_hash` (payload not applied).

Progress is being made on fixes, with Lodestar now working and being added to the checkpointz server. Stefan is actively debugging and expects to push a fix today, with watchtower being enabled for automatic deployment. However, some clients still lack checkpoint sync capability, which is blocking node updates. There's consensus that implementing checkpoint sync functionality should take priority over serving checkpoints.

Action items include Stefan's pending fix, a reminder to create an issue for downscoring problematic behavior, and ongoing work to implement checkpoint sync with a fallback plan to have fixes ready for devnet 1 if needed.

## Participants

- 0xunclebill
- barnabasbusa
- potuz
- stefanbratanov
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T09:23:28.361000+00:00] barnabasbusa: any update <@641548687813902336> <@822741226088169479> ?
[2026-03-11T09:24:08.242000+00:00] barnabasbusa: ### nimbus-geth-1

```
INF 2026-03-11 08:51:34.998+00:00 Slot start topics="beacnde" slot=48698 epoch=1521
    sync="--h--m (9.35%) 0.0000slots/s (UwQQQQQQQw:4553)/opt" peers=8 head=1e084448:4554
..
INF 2026-03-11 08:51:34.998+00:00 Beacon node not in sync; skipping validator duties for now
    topics="beacval" slot=48698 headSlot=4554
..
DBG 2026-03-11 08:50:11.770+00:00 Failed to get block data topics="overseer"
    reason="Failed to receive envelopes on request, reason: (kind: ReceivedErrorResponse,
    responseCode: ServerError, errorMsg: "Unexpected error")"
```

### teku-geth-1

```
2026-03-11 08:51:53.717 ERROR - Internal error while importing execution payload: 7330
    (block root: ca5c31163a311ac61d33ecb76e5a7f482e501b2beede1ad6d489f21b1b566fa4,
     builder index: 18446744073709551615)
...
Caused by: tech.pegasys.teku.spec.logic.common.statetransition.exceptions.BlockProcessingException:
    Bid is not for the right parent block
...
2026-03-11 08:51:47.001 INFO - Syncing *** Slot: 48699, Head slot: 9804, Remaining slots: 38895,
    Target chain: 08cc7a..9fcf (48608) with 1 peers, Connected peers: 4
```
[2026-03-11T09:24:52.260000+00:00] tomi0x: Working on an update will push later today
[2026-03-11T09:41:35.211000+00:00] barnabasbusa: <@358120958726373381> <@737658693331714159> Lighthouse is still the odd one out. It has latest_block_hash = bid.block_hash (payload already applied), while both Prysm and Lodestar agree on latest_block_hash = bid.parent_block_hash (payload not yet applied).
[2026-03-11T09:41:53.531000+00:00] barnabasbusa: Lodestar is now working
[2026-03-11T10:04:11.385000+00:00] stefanbratanov: still debugging, getting somewhere, hopefully today I push a fix
[2026-03-11T10:04:32.874000+00:00] barnabasbusa: do you need watchtower to be enabled on the host?
[2026-03-11T10:04:41.769000+00:00] barnabasbusa: so it could auto roll the changes
[2026-03-11T10:38:03.303000+00:00] stefanbratanov: Yeah, can do it for consensys/master, we already merged my previous PR
[2026-03-11T10:38:43.489000+00:00] barnabasbusa: rolling and enabling watchtower
[2026-03-11T10:57:10.726000+00:00] potuz: Yeah I imagine so, we need to keep an issue of downscoring this behavior eventually before we forget 🙂
[2026-03-11T11:07:59.897000+00:00] barnabasbusa: added lodestar now to the checkpointz server
[2026-03-11T11:08:34.886000+00:00] barnabasbusa: in the upstreams tab you can see which nodes are currently configured
[2026-03-11T11:23:42.555000+00:00] 0xunclebill: yeah well have a fix for this soon. Issue is we still don’t have checkpoint sync so we can’t update our nodes
[2026-03-11T11:23:59.647000+00:00] 0xunclebill: worst case we’ll have the fix for devnet 1
[2026-03-11T11:27:36.979000+00:00] barnabasbusa: I think focusing on able to do checkpoint sync > serving checkpoint should be the prio
[2026-03-11T11:29:53.647000+00:00] 0xunclebill: we have someone on it 🫡
```

</details>
