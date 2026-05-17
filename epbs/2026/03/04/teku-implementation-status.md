# Teku implementation fixes and PR status

**Channel:** epbs | **Date:** 2026-03-04

## Summary

Stefan Bratanov reported progress on fixing a broken Teku implementation, stating he has managed to get it working and plans to raise a PR and merge it to master by end of day. The current version has significant issues, including problems with producing attestations in some cases.

Despite the attestation issues, Bratanov successfully achieved finalization when running Teku with Lodestar and has created PR #10445 in the Consensys/teku repository. The main action item is completing the PR review and merge process to get the fixes into the master branch.

## Participants

- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T12:40:14.934000+00:00] stefanbratanov: Finally managed to make it working for Teku, I just need to raise a PR and merge it and we should be good on master, hopefully by EOD
[2026-03-04T12:40:30.411000+00:00] stefanbratanov: current version is pretty broken
[2026-03-04T12:48:54.071000+00:00] stefanbratanov: There is some issue with producing attestations in some cases, but managed to finalize with lodestar https://github.com/Consensys/teku/pull/10445
```

</details>
