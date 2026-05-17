# Release Tarball Upload Issues and Resolution

**Channel:** epbs | **Date:** 2026-01-06

## Summary

A technical issue occurred during the release process for Ethereum consensus specs v1.7.0-alpha.0, where the tarball upload step failed at the end of the automated release workflow. The failure caused the system to create a duplicate release with the same name rather than uploading the tarballs to the original intended release.

The issue was quickly resolved by manually downloading the tarballs from the duplicate release and re-uploading them to the correct release location. The fix appears to have been straightforward, though the root cause of the initial upload failure remains unclear.

## Participants

- jtraglia
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-06T13:44:40.902000+00:00] stefanbratanov: Looks like it failed at the end when uploading the tarballs 😬
[2026-01-06T13:46:27.791000+00:00] jtraglia: Yeah easy fix. Not sure what happened there 🤷‍♂️
[2026-01-06T13:46:33.293000+00:00] jtraglia: https://github.com/ethereum/consensus-specs/releases/tag/v1.7.0-alpha.0
[2026-01-06T13:47:25.768000+00:00] jtraglia: It uploaded the tarballs to a new release with the same name. So I just downloaded & re-uploaded to the correct one.
```

</details>
