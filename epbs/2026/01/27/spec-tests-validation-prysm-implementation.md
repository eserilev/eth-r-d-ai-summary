# Spec tests validation and Prysm implementation

**Channel:** epbs | **Date:** 2026-01-27

## Summary

terencechain inquired about running spec tests by ignoring the payload component, seeking confirmation that other clients have successfully validated these tests to avoid debugging issues that might stem from either Prysm implementation bugs or spec test problems. jtraglia confirmed this approach is viable and referenced a specific pull request (https://github.com/OffchainLabs/prysm/pull/15312) as an example implementation.

terencechain agreed to test this approach and committed to reporting back on the results, leaving the discussion with a clear action item to validate the spec tests using the payload-ignoring method.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-27T01:39:15.932000+00:00] terencechain: are you saying that these spec tests can be ran today, i just need to ignore the payload
have any clients done that and verify they pass?
wanna avoid looking deeper if it fails then have to figure out whether its a prysm bug or a spec test issue 🙂
[2026-01-27T02:00:17.548000+00:00] jtraglia: Yes, see: https://github.com/OffchainLabs/prysm/pull/15312
[2026-01-27T02:24:32.115000+00:00] terencechain: Ok thanks. I'll try it out and let you know how it goes
```

</details>
