# Prysm Branch Rebase and Builder Tests Fix

**Channel:** epbs | **Date:** 2026-01-31

## Summary

terencechain resolved an issue with builder tests by identifying that they had failed to rebase their branch on top of a specific commit (55fe85c8875037d798504d8975ba6986385c465a) from the OffchainLabs/prysm repository. After performing the proper rebase, the builder tests became visible and continued to pass as expected.

The issue was attributed to user error rather than a technical problem with the codebase, and the resolution confirms that the builder functionality is working correctly in the updated branch.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-31T16:58:31.172000+00:00] terencechain: to wrap this up, this was my fault, i didn't rebase my branch on top of https://github.com/OffchainLabs/prysm/commit/55fe85c8875037d798504d8975ba6986385c465a, after rebasing, i can see the builders tests and they still pass!
```

</details>
