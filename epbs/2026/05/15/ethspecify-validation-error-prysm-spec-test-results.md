# Ethspecify validation error and Prysm spec test results

**Channel:** epbs | **Date:** 2026-05-15

## Summary

terencechain encountered a validation error when running `ethspecify check`, receiving a 404 error for the pyspec.json file for version v1.7.0-alpha.8. The issue was due to ethspecify's automatic updates only running once daily at UTC midnight, and the new version hadn't been processed yet.

jtraglia resolved the issue by manually running the update process, allowing terencechain to successfully complete the validation. Following this fix, terencechain confirmed that Prysm now passes all spec tests for the alpha8 release, with jtraglia noting the quick implementation turnaround.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-15T22:33:17.199000+00:00] terencechain: is this a bug on our end?
```
ethspecify check
Warning: Could not validate exceptions - failed to load pyspec: 404 Client Error: Not Found for url: https://raw.githubusercontent.com/jtraglia/ethspecify/main/pyspec/v1.7.0-alpha.8/pyspec.json
```
[2026-05-15T22:37:46.945000+00:00] jtraglia: It only updates once a day at UTC midnight. Let me run it manually now.
[2026-05-15T22:39:15.575000+00:00] jtraglia: <@363800010518822915> okay try again!
[2026-05-15T22:39:35.219000+00:00] terencechain: thanks, worked!
[2026-05-15T22:40:07.680000+00:00] terencechain: prysm passes alpha8 spec tests
[2026-05-15T22:40:29.579000+00:00] jtraglia: Nice, that was fast. Pandaprize? <@412614104222531604>
```

</details>
