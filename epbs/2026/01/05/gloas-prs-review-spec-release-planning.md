# Gloas PRs Review and Spec Release Planning

**Channel:** epbs | **Date:** 2026-01-05

## Summary

The discussion centers around reviewing and merging Gloas PRs to enable a spec release for ePBS (Enshrined Proposer-Builder Separation) development. jtraglia reminded the team to review open Gloas PRs, particularly PR #4788, with plans to merge several during the week. As ePBS discussions transition from dedicated breakout rooms to ACDT meetings, barnabasbusa requested delivery deadlines for ePBS devnets and proposed a "spec freeze" for devnet 0 once PR #4788 was merged.

Multiple team members agreed on the need for a spec release, though potuz noted that achieving a true spec freeze would be challenging given major incoming changes. They distinguished between Francesco's non-consensus-breaking changes (which are optional) and Justin's changes (which require merging and may need multiple iterations). potuz emphasized that spec releases are essential for client teams to review code in their branches.

The discussion concluded with concrete progress: jtraglia successfully merged PR #4788 and initiated the `v1.7.0-alpha.0` release. terencechain confirmed approval and noted that separate PRs for spec tests would be needed as a follow-up action item.

## Participants

- barnabasbusa
- jtraglia
- potuz
- stefanbratanov
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-05T03:39:11.301000+00:00] jtraglia: Reminder to review some of the open Gloas PRs:
* https://github.com/ethereum/consensus-specs/pulls?q=is%3Apr+is%3Aopen+label%3Agloas
Especially this one:
* https://github.com/ethereum/consensus-specs/pull/4788

I plan to merge several of these this week.
[2026-01-05T11:28:12.364000+00:00] barnabasbusa: As we not gonna have anymore epbs breakout rooms, and we gonna be moving epbs discussion topics to ACDT, it would be good to start having actual delivery deadlines for epbs only devnets. Could I get an update on the latest on the specs? Once we have 4788 merged in, could we get a "spec freeze" for devnet 0?
[2026-01-05T12:37:53.172000+00:00] stefanbratanov: I agree, it would be good to have a specs release this week at some point
[2026-01-05T13:50:29.583000+00:00] terencechain: Sounds good to me
[2026-01-05T14:03:25.692000+00:00] jtraglia: Yes, I plan to make a spec release. I've just been waiting for client teams to approve the PR(s).
[2026-01-05T15:52:31.161000+00:00] potuz: hard to get a spec freeze with such a major change coming in
[2026-01-05T15:52:54.606000+00:00] potuz: feature freeze may be, but still we rely on spec releases to be able to even review code in our branches
[2026-01-05T15:53:38.981000+00:00] potuz: Francesco's changes are kinda irrelevant cause they are not consensus breaking, so we can include them or not, it doesn't matter, but Justin's need to be merged and it will take surely a couple of back and forth to set the spec frozen
[2026-01-05T17:10:04.261000+00:00] jtraglia: I have merged 4788
[2026-01-05T21:12:40.730000+00:00] jtraglia: Started the `v1.7.0-alpha.0` release: https://github.com/ethereum/consensus-specs/actions/runs/20729286454
[2026-01-05T21:15:39.697000+00:00] terencechain: looks good to me, will need to work on spec tests for these in a separate pr
```

</details>
