# Fork Choice Abstraction Layer PR Review

**Channel:** epbs | **Date:** 2026-05-13

## Summary

m.kalinin requested review of PR #5249, which extends the fork choice abstraction layer to facilitate easier upgrades from Phase0 to Gloas. The PR aims to improve readability and testability of the fork choice specification without introducing semantic changes, addressing issues kalinin encountered when working with the fork choice spec in Gloas.

potuz requested to hold the PR until it receives multiple reviews, expressing particular concern about ensuring no unintended changes occur post-Gloas or that any changes are well understood. potuz also referenced ongoing issues with PR #4807 backporting that has caused some spectests to be skipped, which they hope to resolve soon.

The discussion remains open with the main action item being to gather additional reviews before proceeding with the fork choice abstraction changes.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-13T18:24:32.193000+00:00] m.kalinin: It would be great if someone would take a look at this PR: https://github.com/ethereum/consensus-specs/pull/5249
It does an important thing and extends fork choice abstraction layer so it can be modified in Gloas and make the upgradability from Phase0 to Gloas easier than it is today.

Impls doesn't need to change anything as ultimately this PR doesn't introduce any semantics change and rather addresses readability and testability issues of the spec which I personally had when started to read and work with the fork choice spec in Gloas
[2026-05-13T20:46:29.874000+00:00] potuz: can we hold that one until we get a few views? I don't mind if the logic is slightly changed pre-Gloas, but I want to really check that nothing changes post-Gloas, or that at least I understand the changes
[2026-05-13T20:51:13.074000+00:00] potuz: For reference, the backporting caused in <https://github.com/ethereum/consensus-specs/pull/4807> still has us skipping some spectests. I hope I can unskip them soon.
```

</details>
