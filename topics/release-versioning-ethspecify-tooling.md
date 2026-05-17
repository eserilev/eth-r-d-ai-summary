# Release v1.7.0-alpha.8 and ethspecify tooling

---

## 2026-05-15 (epbs)

jtraglia initiated the release process for Ethereum consensus-specs v1.7.0-alpha.8, which completed successfully after approximately 6 hours. The release is now available on GitHub.

During testing, terencechain encountered an issue with the ethspecify tooling where it couldn't validate exceptions due to a 404 error when trying to load the pyspec file for the new version. jtraglia explained that the ethspecify tool normally updates once daily at UTC midnight, but manually triggered an update to resolve the immediate issue. 

After the manual update, terencechain confirmed that the ethspecify tool was working correctly and reported that Prysm client successfully passes all alpha8 spec tests, indicating good compatibility with the new release.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-15T15:18:48.564000+00:00] jtraglia: <@&1417820113981145228> The [release action for v1.7.0-alpha.8](https://github.com/ethereum/consensus-specs/actions/runs/25925661968) was just started. If successful, it will be ready in ~6 hours. In this time, if we learn that something is missing, we can restart.
[2026-05-15T20:52:01.938000+00:00] jtraglia: The release is out: https://github.com/ethereum/consensus-specs/releases/tag/v1.7.0-alpha.8
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

