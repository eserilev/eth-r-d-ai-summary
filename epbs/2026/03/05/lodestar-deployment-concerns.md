# Lodestar Deployment Concerns

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around deployment concerns for the epbs-devnet-0 branch. nflaig inquired about auto-deployment when pushing to this branch, mentioning they have work-in-progress code for range sync, envelope by range, and some ptc fixes. They expressed caution about pushing changes without knowing the deployment behavior.

barnabasbusa confirmed that auto-deployment is enabled through watchtower on all nodes. nflaig expressed relief at asking first, noting it wouldn't be ideal to deploy until they can ensure the system can properly return to head after a restart. In response to these concerns, barnabasbusa agreed to and subsequently disabled watchtower on all nodes to prevent unintended automatic deployments.

## Participants

- barnabasbusa
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T11:00:29.685000+00:00] nflaig: does it auto deploy if we push to epbs-devnet-0 branch?
[2026-03-05T11:00:46.124000+00:00] nflaig: we do have wip for range sync and envelope by range
[2026-03-05T11:00:57.119000+00:00] nflaig: also some ptc fixes but I guess not needed right now
[2026-03-05T11:31:16.302000+00:00] nflaig: that's why I asked if it auto deploys, will not push to that branch until we know for sure
[2026-03-05T11:33:25.921000+00:00] barnabasbusa: yes
[2026-03-05T11:33:49.055000+00:00] nflaig: oh damn good thing I asked
[2026-03-05T11:34:07.736000+00:00] barnabasbusa: we got watchtower (auto deployment enabled)
[2026-03-05T11:34:12.224000+00:00] nflaig: probably not ideal until we know that we can get back to head after restart
[2026-03-05T11:34:17.500000+00:00] barnabasbusa: ok.
[2026-03-05T11:34:20.575000+00:00] barnabasbusa: I'm happy to disable it
[2026-03-05T11:34:59.523000+00:00] barnabasbusa: ok disabled watchtower on all nodes
```

</details>
