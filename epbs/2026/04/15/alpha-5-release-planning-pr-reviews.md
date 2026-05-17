# Alpha.5 Release Planning and PR Reviews

**Channel:** epbs | **Date:** 2026-04-15

## Summary

The discussion centers around planning for the alpha.5 release, with jtraglia seeking feedback on PR #5113 from clients and asking what should be included beyond PR #5094. Potuz indicates the change would be trivial for their client implementation, while noting that most of the work is being handled by a single developer.

barnabasbusa identifies the "gloas genesis" PR (#5067) as another item that should be included in the alpha.5 release, though notes it may need rebasing if PR #5094 gets merged first. jtraglia acknowledges they'll handle the rebasing issue as needed.

The discussion concludes with an open question about the status and readiness of PR #5094, with no clear resolution provided in the messages. Key action items include getting client feedback on PR #5113 and determining the final status of PR #5094 for the alpha.5 release.

## Participants

- barnabasbusa
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-15T16:04:21.142000+00:00] jtraglia: How do clients feel about this change (https://github.com/ethereum/consensus-specs/pull/5113). Seems reasonable to me, but might be an annoying change for clients at this point in time. If you don't think it will be too bad, we can merge it. cc <@881905303011086387>
[2026-04-15T16:28:51.480000+00:00] jtraglia: <@&1417820113981145228> other than 5094, is there anything we want to see in the alpha.5 release?
[2026-04-15T16:32:40.707000+00:00] potuz: should be trivial for us but cc <@363800010518822915> who's implementing most alone.
[2026-04-15T16:35:36.811000+00:00] barnabasbusa: yes the gloas genesis pr
[2026-04-15T16:37:14.741000+00:00] jtraglia: This one? https://github.com/ethereum/consensus-specs/pull/5067
[2026-04-15T16:39:13.964000+00:00] barnabasbusa: yes
[2026-04-15T16:40:50.836000+00:00] barnabasbusa: might need a rebase if 5094 gets merged
[2026-04-15T16:42:07.398000+00:00] jtraglia: Definitely. Okay we'll deal with this too.
[2026-04-15T16:42:32.905000+00:00] jtraglia: How are we feeling about 5094?
```

</details>
