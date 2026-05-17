# Reference Tests Generation and Availability

**Channel:** epbs | **Date:** 2026-04-17

## Summary

The discussion centers around the generation and availability of reference tests for Ethereum consensus specifications. jtraglia provided timelines for test availability, initially offering reference tests for the master branch within an hour, then expanding to include tests for all configurations (general/minimal/mainnet) within 5 hours through GitHub Actions workflows.

nc1234 specifically requested mainnet tests, highlighting a critical issue where mainnet's proposer boost test was using a pre-Gloas cutoff time that affected mainnet configurations but not minimal ones. This issue was previously addressed in PR #5095, demonstrating the importance of running tests across different network configurations to catch environment-specific bugs.

jtraglia acknowledged the mainnet testing concern and committed to investigating the issue further, while providing the broader test suite that would include the necessary mainnet configurations to prevent similar problems in the future.

## Participants

- jtraglia
- nc1234
- sproul

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-17T00:00:13.164000+00:00] sproul: I guess just minimal would be fine yeah
[2026-04-17T00:02:26.981000+00:00] jtraglia: Reference tests for master will be available here in ~1 hour.
https://github.com/ethereum/consensus-specs/actions/runs/24540424255
[2026-04-17T00:02:28.743000+00:00] nc1234: Would be great if you can also do mainnet. Cuz there was an issue with mainnet's proposer boost test using pre-gloas cutoff time that didn't happen in minimal but mainnet. Was fixed in https://github.com/ethereum/consensus-specs/pull/5095
[2026-04-17T00:03:23.869000+00:00] jtraglia: Reference tests for everything (general/minimal/mainnet) will be available here in ~5 hours.
https://github.com/ethereum/consensus-specs/actions/runs/24540447082
[2026-04-17T00:05:12.808000+00:00] jtraglia: Let me look into this. Thanks for letting me know!
```

</details>
