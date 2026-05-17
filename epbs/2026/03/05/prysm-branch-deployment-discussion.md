# Prysm Branch Deployment Discussion

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around deploying a specific Prysm branch (`epbs-devnet-0-sync`) for a development network. Barnabasbusa offered to roll out the branch, while potuz clarified it was already deployed manually since yesterday and running at 85% capacity. Barnabasbusa proceeded to deploy it through the official control center to replace the "hacky manual one-off" deployment, despite potuz's request not to restart nodes unnecessarily.

The conversation revealed some process issues, with qu0b and barnabasbusa questioning why there's a separate sync-specific image rather than merging the changes into the main `epbs-devnet-0` branch. Potuz explained this was because the sync functionality was added "at the very last second without anyone reading it," indicating a rushed implementation that bypassed normal review processes.

## Participants

- barnabasbusa
- potuz
- qu0b

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T10:29:43.159000+00:00] barnabasbusa: <@755590043632140352> need me to roll specific branch for prysm now?
[2026-03-05T10:29:50.141000+00:00] barnabasbusa: I'm not sure whats the latest right now
[2026-03-05T10:30:48.855000+00:00] potuz: If there's a chance that we fall and need to sync again then yes `epbs-devnet-0-sync`
[2026-03-05T10:32:25.825000+00:00] barnabasbusa: rolling out that branch on all without the commit hash
[2026-03-05T10:32:52.332000+00:00] potuz: It is already rolled
[2026-03-05T10:33:01.446000+00:00] potuz: We're at 85%
[2026-03-05T10:33:10.599000+00:00] potuz: We're running that since yesterday
[2026-03-05T10:33:21.972000+00:00] barnabasbusa: right, but now we have it rolled out from the control center
[2026-03-05T10:33:23.720000+00:00] potuz: Don't restart our nodes unnecessarily 🙂
[2026-03-05T10:33:26.235000+00:00] barnabasbusa: not that hacky manual one off
[2026-03-05T10:33:26.899000+00:00] qu0b: why is there a different image for sync
[2026-03-05T10:33:49.786000+00:00] barnabasbusa: yeah thats what I've been asking why not just merge this in to epbs-devnet-0 branch
[2026-03-05T10:33:55.662000+00:00] potuz: Because it was added at the very last second without anyone reading it
```

</details>
