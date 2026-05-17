# Log Retrieval for Bug Investigation

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz is investigating a bug and realizes they may have wiped the logs when clearing the database, which they needed to retrigger the same issue that barnabasbusa previously encountered. Barnabasbusa suggests artificially creating the conditions by having 3 consecutive slots missed and restarting nodes, but potuz notes this needs to be under justified conditions and involves too many variables to control properly.

Barnabasbusa successfully retrieves the logs from the Docker container path `/var/lib/docker/containers/<container-id>/<container-id>-json.log` and shares them with potuz, including logs from before the crash and from when a new commit was rolled out. The logs reveal excessive DEBUG logging for "Could not read parent slot from forkchoice; trying DB fallback for state lookup" messages, and potuz confirms the retrieved logs are very helpful for the investigation, noting they clearly describe the situation when combined with additional context (referenced as "Dora").

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T11:59:28.023000+00:00] potuz: <@412614104222531604> when I wiped the dbase did I wipe also all previous logs?
[2026-03-05T11:59:52.854000+00:00] potuz: I'm trying to retrigger the same issue you hit
[2026-03-05T12:00:06.456000+00:00] barnabasbusa: we can try to have 3 consequity slots missed 😄
[2026-03-05T12:00:08.144000+00:00] barnabasbusa: and restart
[2026-03-05T12:00:38.889000+00:00] barnabasbusa: in one min there will be 3 prysm proposers
[2026-03-05T12:00:43.973000+00:00] barnabasbusa: so just stop the validator client on 1/3
[2026-03-05T12:00:57.086000+00:00] barnabasbusa: then quickly restart one of the beacon nodes
[2026-03-05T12:02:18.183000+00:00] potuz: nah needs to be justified, I could time it, but too many variables to check. So did I wipe the logs?
[2026-03-05T12:02:31.025000+00:00] potuz: I should have downloaded them before restarting crap 🙁
[2026-03-05T12:02:56.262000+00:00] barnabasbusa: ` /var/lib/docker/containers/<container-id>/<container-id>-json.log`
[2026-03-05T12:02:58.717000+00:00] barnabasbusa: can you check here?
[2026-03-05T12:02:59.857000+00:00] potuz: also when it's too stable with all Prysm participating even the reorgs because of bad attestations are less so the current logs are less useful
[2026-03-05T12:03:01.308000+00:00] barnabasbusa: which node?
[2026-03-05T12:03:22.341000+00:00] potuz: the beacon node I removed /data/prysm but not var/lib...
[2026-03-05T12:04:05.112000+00:00] barnabasbusa: e.g on prysm-geth-1:

`/var/lib/docker/containers/5dfac7d60f18a462adcb1016ae4bd95a4e1da1602b378b5cdda4733e25faea5b/5dfac7d60f18a462adcb1016ae4bd95a4e1da1602b378b5cdda4733e25faea5b-json.log`
[2026-03-05T12:04:30.823000+00:00] barnabasbusa: its the raw log file so it might be weirdly formatted due to tty
[2026-03-05T12:05:01.483000+00:00] barnabasbusa: hm it might be wiped
[2026-03-05T12:05:13.121000+00:00] barnabasbusa: But I still can copy the logs for you
[2026-03-05T12:05:18.055000+00:00] barnabasbusa: As I have in my terminal 😄
[2026-03-05T12:06:49.639000+00:00] barnabasbusa: 
[2026-03-05T12:06:49.916000+00:00] barnabasbusa: here you go
[2026-03-05T12:06:50.791000+00:00] potuz: you have the logs before the crash?
[2026-03-05T12:06:54.945000+00:00] barnabasbusa: yup
[2026-03-05T12:07:06.476000+00:00] potuz: thanks so much!
[2026-03-05T12:08:04.207000+00:00] barnabasbusa: this is pretty excessive `DEBUG stategen: Could not read parent slot from forkchoice; trying DB fallback for state lookup`
[2026-03-05T12:08:10.616000+00:00] barnabasbusa: not sure if its really needed to log it this many times 😄
[2026-03-05T12:10:13.724000+00:00] barnabasbusa: Here is one more bunch
[2026-03-05T12:10:18.201000+00:00] barnabasbusa: But this is as early as it gets
[2026-03-05T12:10:21.509000+00:00] barnabasbusa: its one minute before
[2026-03-05T12:10:30.134000+00:00] barnabasbusa: this is when the new commit was rolled out
[2026-03-05T12:11:44.208000+00:00] potuz: This log was very very helpful, thanks a lot
[2026-03-05T12:11:50.545000+00:00] potuz: Dora plus this describes the situation clearly
```

</details>
