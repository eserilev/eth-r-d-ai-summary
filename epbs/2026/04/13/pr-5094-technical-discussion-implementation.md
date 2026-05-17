# PR 5094 implementation timeline and release planning

**Channel:** epbs | **Date:** 2026-04-13

## Summary

The discussion centers around PR 5094, with strong support from most client teams for the proposed changes. The team is considering implementation for epbs-devnet-2 and plans to make a decision during an ACDT (All Core Devs Testing) call. Terencechain has created a draft implementation branch and shared a Prysm pull request for the changes.

The release timeline was established with jtraglia proposing to get a new consensus layer spec version out within 48 hours if approved, following a process of merging the PR, generating nightly reference tests, and confirming at least one client passes the updated tests. However, potuz emphasized the need for thorough review before rushing to merge, noting that a release should come before passing tests and that all subtleties need proper coverage.

Action items include nflaig posting a document detailing additional benefits and trade-offs from the change, thorough PR review by all team members, and terencechain testing his draft branch against nightly reference tests once available. The team agreed that even if Prysm passes tests, this shouldn't signal rushing the merge process.

## Participants

- barnabasbusa
- jtraglia
- nflaig
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T13:28:16.960000+00:00] barnabasbusa: got a branch for it?
[2026-04-13T13:28:25.282000+00:00] barnabasbusa: I feel like this is epbs-devnet-2 terretory
[2026-04-13T13:28:31.408000+00:00] barnabasbusa: I think we can make the call on this today on acdt
[2026-04-13T13:28:48.288000+00:00] barnabasbusa: but based on what I have read above, it seems like most client teams are strongly in favor of this change.
[2026-04-13T13:31:01.142000+00:00] terencechain: Yes I agree. I'll push once I get Infront of computer
[2026-04-13T13:31:32.907000+00:00] barnabasbusa: <@592004585506340885> if we do decide in favor of 5094, how soon can we get a new cl spec version out?
[2026-04-13T13:48:15.624000+00:00] terencechain: https://github.com/OffchainLabs/prysm/pull/16658
[2026-04-13T14:04:26.823000+00:00] jtraglia: If we decide to merge this, I think we can get a release out in < 48 hours. I would like to (1) merge the PR, (2) generate nightly reference tests for it, and (3) confirm that one or more clients pass the updated tests. Then, if successful, we'll make a release.
[2026-04-13T15:03:58.920000+00:00] jtraglia: Sorry, the end of the breakout call was very rushed. If there are any concerns/comments with the timeline for merging that PR & making a release, I'd be happy to chat about it here.
[2026-04-13T15:08:36.813000+00:00] nflaig: I will post a document with other wins we get from this change + trade-offs later today/tomorrow, it seemed though that everyone in the call had a good understanding of those already
[2026-04-13T15:11:48.455000+00:00] potuz: I believe we need a release before passing tests :). I will focus on reviewing the PR and we should all do that, but please lets not rush on merging it until we've covered all subtleties.
[2026-04-13T15:15:05.192000+00:00] jtraglia: Yeah I won't rush it. But it would be nice if a single client passed tests, as a sanity check.
[2026-04-13T15:23:38.650000+00:00] terencechain: if you give me the nightly reference, i can see if my draft branch passes it, but even prysm passes it, it's not a signal to rush it
[2026-04-13T18:12:43.900000+00:00] nflaig: #5094 review lounge
```

</details>
