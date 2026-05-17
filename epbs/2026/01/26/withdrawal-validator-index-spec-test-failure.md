# Process withdrawal spec tests implementation status

**Channel:** epbs | **Date:** 2026-01-26

## Summary

terencechain inquired about the implementation status of process withdrawal spec tests and noted an odd `execution_payload.ssz_snappy` file in the current folder structure. jtraglia confirmed that these tests have been merged in PR #4830 but are not yet included in an official release, and acknowledged the file structure issue that terencechain observed.

While terencechain initially planned to wait for the alpha2 release rather than testing the alpha1 version, jtraglia encouraged running the tests despite their preliminary status, noting that the development team isn't completely confident in their correctness yet. This suggests the tests would benefit from additional validation before the next release.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-26T21:55:46.668000+00:00] terencechain: has process withdrawal spec test been implemented? anyone pass them?
[2026-01-26T21:56:29.468000+00:00] terencechain: if yes, i have a question, the current folder contains `execution_payload.ssz_snappy`, which is a bit odd, not clear to me the intent behind that object
[2026-01-26T21:57:18.078000+00:00] jtraglia: These tests have been merged: https://github.com/ethereum/consensus-specs/pull/4830
[2026-01-26T21:57:22.699000+00:00] jtraglia: They do not yet exist in a release.
[2026-01-26T21:57:39.072000+00:00] terencechain: ok thanks, ill wait for the new release then. For now, i wont try to run it
[2026-01-26T21:57:39.520000+00:00] jtraglia: And yes, I've noticed this too. I think it's been fixed since alpha.1 but I will double check.
[2026-01-26T21:57:54.163000+00:00] terencechain: alpha1 is the one im looking at.. ill wait for alpha2
[2026-01-26T21:58:02.972000+00:00] jtraglia: If you could try to run them, that would be great actually. We're not 100% sure they're correct.
```

</details>
