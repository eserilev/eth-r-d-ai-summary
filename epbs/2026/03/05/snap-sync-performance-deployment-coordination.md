# Snap sync performance and deployment coordination

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around snap sync performance issues and coordinating deployment of updated Prysm clients on what appears to be an EPBS (Enshrined Proposer-Builder Separation) devnet. Potuz reports unexpectedly slow snap sync times for an empty state and confirms that database deletion is required when deploying new images. The team is working with the "epbs-devnet-0-sync" branch, with Potuz and Terence making progress toward chain head (Terence synced, Potuz at block 2391 then 2903).

The deployment process involves automatic monitoring of specific branch tags, with barnabasbusa explaining that triggering a new image build will auto-update deployed nodes. The recommended approach is to first test the sync fixes on one node that pari previously rolled out, then merge the changes to the main epbs-devnet-0 branch to update the remaining two nodes. Database wiping can be done manually by stopping the beacon node, removing contents from /data/prysm/*, and restarting the container.

**Action items:** Potuz requests someone to trigger a build with the latest commits and suggests merging his recent PRs. An open issue remains with geth not syncing properly, which Potuz notes is "above my paygrade."

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T00:25:04.813000+00:00] potuz: how can it take so long to snapsync an empty state
[2026-03-05T00:26:15.607000+00:00] potuz: <@199561711278227457> when you deploy a new image you have to delete the db?
[2026-03-05T00:31:03.073000+00:00] potuz: <@412614104222531604> I see you online
[2026-03-05T00:31:27.237000+00:00] barnabasbusa: sadly yes
[2026-03-05T00:31:35.638000+00:00] potuz: we're up to head here locally (Terence is, I'm at 2391) soon can you redeploy Prysm?
[2026-03-05T00:31:47.866000+00:00] barnabasbusa: It’s automatic
[2026-03-05T00:31:57.562000+00:00] potuz: it's a different branch
[2026-03-05T00:32:01.843000+00:00] barnabasbusa: we are just puppets pretending that we are doing work
[2026-03-05T00:32:01.911000+00:00] potuz: it's epbs-devnet-0-sync
[2026-03-05T00:32:11.576000+00:00] barnabasbusa: didn’t pari roll that out?
[2026-03-05T00:32:33.556000+00:00] potuz: yeah but we pushed two commits, wait for confirmation I'll be at head in a couple of minutes
[2026-03-05T00:32:47.162000+00:00] potuz: Terence is retrying to reconfirm he can get there
[2026-03-05T00:33:07.384000+00:00] potuz: But as soon as I have my local node running I know I can serve Prysm nodes if they can discover me
[2026-03-05T00:33:15.697000+00:00] barnabasbusa: right so it will roll out the new branch
[2026-03-05T00:33:15.909000+00:00] potuz: 2903
[2026-03-05T00:33:47.935000+00:00] potuz: explain it to me that I barely understand smoke signals
[2026-03-05T00:34:05.016000+00:00] barnabasbusa: Whichever branch is rolled out will get auto updated
[2026-03-05T00:34:13.005000+00:00] barnabasbusa: we monitor the tag
[2026-03-05T00:34:20.542000+00:00] potuz: how do we do it then?
[2026-03-05T00:34:36.031000+00:00] potuz: to wipe the node and restart it with the newest commits?
[2026-03-05T00:34:36.947000+00:00] barnabasbusa: just trigger a new image build of the branch
[2026-03-05T00:34:50.411000+00:00] potuz: and it wipes all prysm nodes?
[2026-03-05T00:35:02.379000+00:00] barnabasbusa: https://discord.com/channels/595666850260713488/892088344438255616/1359822239087722598
[2026-03-05T00:35:17.935000+00:00] barnabasbusa: just do the one pari rolled the new img out on
[2026-03-05T00:35:57.793000+00:00] barnabasbusa: once you confirm that’s working just merge in your sync changed to epbs-devnet-0 branch, and the other two will also be rolled with the fixes
[2026-03-05T00:37:48.794000+00:00] barnabasbusa: you can nuke the db by stopping beacon, sudo rm everything from inside /data/prysm/* and restart container
[2026-03-05T00:38:43.432000+00:00] potuz: ok <@363800010518822915> can you trigger the build with the commit
[2026-03-05T00:38:44.148000+00:00] potuz: ?
[2026-03-05T00:39:00.224000+00:00] potuz: I suggest you also merge my last PRs
[2026-03-05T00:41:48.004000+00:00] potuz: I am up to head on Prysm, but geth is not syncing for some reason, that's above my paygrade
```

</details>
