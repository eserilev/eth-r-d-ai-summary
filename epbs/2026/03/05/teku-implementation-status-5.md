# Teku client sync issues and improvements

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Stefan has improvements to Teku client sync functionality in a branch that he plans to merge soon after team review. Barnabas offers to test the changes on devnet and attempts both forward sync and checkpoint sync approaches. While Stefan indicates forward sync should work with available peers serving range RPCs, he's uncertain about checkpoint sync functionality.

During testing, Barnabas encounters persistent "Fork choice process head failed" errors with the message "Unable to produce state for block at slot 321" even when performing a fresh genesis sync with a clean database. Stefan initially suspects this might be related to state regeneration after following an incorrect chain during reorgs, which the current implementation cannot handle well. However, the error persists even during genesis sync, indicating a more fundamental issue.

The team identifies that the problem can be reproduced locally using Kurtosis with the specified devnet configuration. Stefan requests access to the Teku logs to investigate the fork choice errors further, as this represents an unexpected issue that needs resolution before the sync improvements can be considered stable.

## Participants

- barnabasbusa
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T14:19:34.562000+00:00] stefanbratanov: My current branch from that PR should be much better than yesterday, I will merge to master soon when team looks at it
[2026-03-05T14:20:00.264000+00:00] barnabasbusa: happy for me to roll it out on devnet?
[2026-03-05T14:20:02.898000+00:00] barnabasbusa: can you sync?
[2026-03-05T14:48:47.150000+00:00] stefanbratanov: checkpoint sync?
[2026-03-05T14:50:02.330000+00:00] barnabasbusa: any
[2026-03-05T14:50:03.146000+00:00] barnabasbusa: 😂
[2026-03-05T14:51:27.997000+00:00] stefanbratanov: forward sync should work given we have peers who serve the by range RPCs
[2026-03-05T14:51:57.980000+00:00] stefanbratanov: I haven't checked if checkpoint sync work
[2026-03-05T14:56:45.486000+00:00] barnabasbusa: ok
[2026-03-05T14:57:01.231000+00:00] barnabasbusa: I can try to forward sync one and checkpoint sync the other
[2026-03-05T15:00:29.634000+00:00] barnabasbusa: teku-geth-1 is now genesis syncing
[2026-03-05T15:00:41.136000+00:00] barnabasbusa: `teku/v22.1.1+3336-g8c9a75d50a/linux-x86_64/-eclipseadoptium-openjdk64bitservervm-java-21`
[2026-03-05T15:05:52.247000+00:00] barnabasbusa: ```
2026-03-05 15:05:35.028 ERROR - Fork choice process head failed
java.util.concurrent.CompletionException: java.lang.IllegalStateException: Unable to produce state for block at slot 321 (0xb319327d39615f9cb2edc2848da7f0d99c05499805dc608262f6c999d8224c1a)
```
[2026-03-05T15:05:56.236000+00:00] barnabasbusa: still seeing this
[2026-03-05T15:07:46.014000+00:00] stefanbratanov: i guess it's trying to regenerate states after reorg or something after following incorrect chain, we currently can't do that really
[2026-03-05T15:09:09.883000+00:00] stefanbratanov: shouldn't see this if you genesis sync
[2026-03-05T15:09:23.804000+00:00] barnabasbusa: im nuking the db
[2026-03-05T15:09:28.271000+00:00] barnabasbusa: so its starting fresh
[2026-03-05T15:10:55.873000+00:00] stefanbratanov: it's a good test to see if we can sync from genesis
[2026-03-05T15:11:16.160000+00:00] barnabasbusa: ah you are right
[2026-03-05T15:11:23.994000+00:00] barnabasbusa: i accidently enabled checkpoitn sync
[2026-03-05T15:11:27.513000+00:00] barnabasbusa: genesis syncing now
[2026-03-05T15:11:35.121000+00:00] barnabasbusa: ```2026-03-05 15:11:23.057 INFO  - Syncing     *** Slot: 7397, Head slot: 550, Remaining slots: 6847, Target chain: 34ffc3..7476 (7328) with 2 peers, Connected peers: 3
2026-03-05 15:11:23.057 INFO  - Sync Info   *** Range: 551 - 675 (5 batches), Downloading: 75 slots (3 batches), Ready: 50 slots (2 batches), Batch import: waiting
```
[2026-03-05T15:11:45.804000+00:00] barnabasbusa: looks like indeed no fork choice errors now
[2026-03-05T15:12:29.042000+00:00] barnabasbusa: spoke too soon 

```2026-03-05 15:12:11.008 ERROR - Fork choice process head failed
java.util.concurrent.CompletionException: java.lang.IllegalStateException: Unable to produce state for block at slot 321 (0xb319327d39615f9cb2edc2848da7f0d99c05499805dc608262f6c999d8224c1a)
```
[2026-03-05T15:12:33.270000+00:00] barnabasbusa: still seeing this :/
[2026-03-05T15:12:50.119000+00:00] barnabasbusa: but head seems to climb still
[2026-03-05T15:13:08.568000+00:00] stefanbratanov: damn, ok, need to look at this, can you pm me teku logs dump
[2026-03-05T15:13:21.720000+00:00] barnabasbusa: `ssh devops@teku-geth-1.srv.epbs-devnet-0.ethpandaops.io`
[2026-03-05T15:14:18.853000+00:00] stefanbratanov: cool, I will ssh
[2026-03-05T15:15:32.904000+00:00] barnabasbusa: ```yaml
participants:
  - cl_type: teku
    cl_image: ethpandaops/teku:StefanBratanov-devnet-0-changes

network_params:
  network: epbs-devnet-0

global_log_level: debug
```

Or you can run this locally and should be able to repro
[2026-03-05T15:17:02.572000+00:00] barnabasbusa: hold off with that kt test 😂 somethign is borked
[2026-03-05T15:34:50.292000+00:00] barnabasbusa: kurtosis should work now too
[2026-03-05T15:36:53.624000+00:00] barnabasbusa: was able to to repro locally immediately with kurtosis
```

</details>
