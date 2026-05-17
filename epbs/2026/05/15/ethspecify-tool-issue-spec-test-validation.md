# ethspecify Tool Issue and Spec Test Validation

**Channel:** epbs | **Date:** 2026-05-15

## Summary

terencechain encountered a 404 error when running `ethspecify check`, which failed to load the pyspec file for version v1.7.0-alpha.8 from the GitHub repository. The error indicated that the validation process couldn't access the required pyspec.json file needed for exception validation.

jtraglia resolved the issue by explaining that the pyspec updates automatically only once daily at UTC midnight, but manually triggered an update to make the v1.7.0-alpha.8 pyspec available immediately. After the manual update, terencechain confirmed the tool worked correctly and reported that Prysm successfully passes the alpha8 spec tests, demonstrating quick implementation progress on the new specification version.

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
