# Release build failure and recovery

**Channel:** epbs | **Date:** 2026-02-03

## Summary

The Ethereum consensus specs release v1.7.0-alpha.2 initially failed during the automated build process. Stefan Bratanov shared a GitHub Actions link showing the failed build job, while jtraglia acknowledged the issue and indicated it was likely a simple fix, noting that they were still working through ongoing issues with the release process.

Jtraglia took manual action to rebuild the tests and tarballs, estimating completion within approximately 1.5 hours. The manual rebuild was successful, and the release was made available at the GitHub repository approximately 5 hours after the initial failure was reported. The incident highlights ongoing stability challenges with the automated release pipeline that the team continues to address.

## Participants

- hangleang
- jtraglia
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-03T09:19:59.484000+00:00] hangleang: the release has failed
[2026-02-03T09:38:23.463000+00:00] stefanbratanov: Hopefully build can recover https://github.com/ethereum/consensus-specs/actions/runs/21597159454/job/62232722497
[2026-02-03T12:51:38.825000+00:00] jtraglia: Will fix shortly. Probably simple. Still ironing out issues with this. Sorry about that.
[2026-02-03T13:11:53.524000+00:00] jtraglia: Manually rebuilding tests & tarballs. Should be ready in ~1.5 hours.
[2026-02-03T14:34:01.771000+00:00] jtraglia: <@&1417820113981145228> the release is available: https://github.com/ethereum/consensus-specs/releases/tag/v1.7.0-alpha.2
```

</details>
