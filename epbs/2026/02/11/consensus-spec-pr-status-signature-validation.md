# Consensus spec PR status and signature validation

**Channel:** epbs | **Date:** 2026-02-11

## Summary

The discussion centers around the status of consensus spec PR #4897, with potuz seeking an update and emphasizing that signature validation cannot be ignored. Ralexstokes indicates the PR is mostly ready but needs tightened tests, and commits to moving it forward with plans to merge by end of week in collaboration with Justin.

Regarding timeline, ralexstokes suggests the changes are suitable for devnet-1 or later rather than devnet-0, noting that while forbidding pubkey duplication is preferable, it wouldn't break functionality if duplicates were allowed. Potuz agrees the changes are functionally irrelevant for devnet-0 since there won't be deposits, but argues against merging incorrect code that would require future modifications, preferring to implement the proper signature validation from the start.

**Action items:** Ralexstokes to tighten up tests and work with Justin to merge the PR by end of week, targeting devnet-1 or later implementation.

## Participants

- potuz
- ralexstokes

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-11T17:53:08.052000+00:00] potuz: <@539495253418180618> what's the status for https://github.com/ethereum/consensus-specs/pull/4897
[2026-02-11T17:53:45.573000+00:00] potuz: I replied to your latest comment here, can add it there if you want, but defniitely can't just ignore the signature check.
[2026-02-11T17:54:28.247000+00:00] ralexstokes: i think we need to tighten up the tests a bit, but mostly good from my perspective
[2026-02-11T17:54:46.455000+00:00] ralexstokes: i can try to move this forward today and can work w/ justin to get this merged by EOW
[2026-02-11T17:54:54.514000+00:00] ralexstokes: is this for devnet-0 or after?
[2026-02-11T17:55:10.796000+00:00] ralexstokes: seems fine for devnet-1 or later as long as no one trolls devnet-0
[2026-02-11T17:55:39.938000+00:00] ralexstokes: (also while i agree its better if we forbid pubkey duplication, i dont think anything would actually break)
[2026-02-11T18:11:09.827000+00:00] potuz: it's functionally irrelevant for devnet zero cause we won't have deposits, but I don't want to start merging incorrect code and keep track that we need to modify it.
```

</details>
