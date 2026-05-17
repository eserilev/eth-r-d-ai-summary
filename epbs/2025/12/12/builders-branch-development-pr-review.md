# Builders branch development and PR review

**Channel:** epbs | **Date:** 2025-12-12

## Summary

The team has moved the work-in-progress builders branch from jtraglia's personal fork to the main ethereum organization repository. jtraglia is actively working on getting the tests to pass and plans to push the branch into a ready-to-review state as soon as possible, expressing satisfaction with the changes made so far.

At potuz's suggestion to improve the review process, jtraglia created a draft PR (#4787) even though the tests are still failing and the work remains incomplete. jtraglia also emphasized the urgent need to merge two existing PRs (#4765 and #4766), expressing frustration with the current version of the code. While potuz had already reviewed these PRs and found them acceptable, jtraglia noted that additional developer reviews are needed and that several changes have been made since potuz's last review.

The main action items are getting additional reviews on the existing PRs #4765 and #4766, and gathering feedback on the new draft PR #4787 for the builders branch development.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T15:06:59.702000+00:00] jtraglia: FYI we've moved the WIP builders branch from my fork to the ethereum org:
* https://github.com/ethereum/consensus-specs/compare/master...builders
[2025-12-12T15:07:20.373000+00:00] jtraglia: Still very much a work in progress. I'm going to work on getting tests passing today.
[2025-12-12T15:07:29.820000+00:00] jtraglia: Any feedback would be appreciated.
[2025-12-12T15:07:46.390000+00:00] jtraglia: So far I like the changes 🙂
[2025-12-12T15:08:31.563000+00:00] jtraglia: I will push to get this into a ready-to-review state asap
[2025-12-12T15:10:44.646000+00:00] potuz: Can you open a PR, even if draft? it makes reviewing it much better
[2025-12-12T15:10:52.072000+00:00] potuz: I can review this afternoon
[2025-12-12T17:00:50.892000+00:00] jtraglia: Will make it into a PR. But just know that it's still a WIP and tests will fail.
[2025-12-12T17:01:57.434000+00:00] jtraglia: Also it would be **really** great if we could get these PRs merged soon.
* https://github.com/ethereum/consensus-specs/pull/4765
* https://github.com/ethereum/consensus-specs/pull/4766
[2025-12-12T17:02:16.847000+00:00] jtraglia: I hate working with the existing version of it. It's awful.
[2025-12-12T17:03:05.707000+00:00] potuz: Didn't I review those? they seem good to me
[2025-12-12T17:03:19.286000+00:00] jtraglia: Yes you did. But I need other devs to review it too.
[2025-12-12T17:03:28.619000+00:00] jtraglia: Also, there have been several changes since your last review.
[2025-12-12T17:19:37.926000+00:00] jtraglia: https://github.com/ethereum/consensus-specs/pull/4787
[2025-12-12T17:21:22.276000+00:00] jtraglia: Please leave as much feedback as you want. Happy to take suggestions here too.
```

</details>
