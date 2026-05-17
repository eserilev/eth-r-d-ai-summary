# Branch Management and Code Review Discussion

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around branch management for an EPBS (Enshrined Proposer-Builder Separation) devnet implementation. barnabasbusa questions why the team is creating additional branches instead of pushing directly to the existing "epbs devnet 0" branch, noting that the original branch isn't currently functional anyway.

potuz clarifies that recent syncing changes haven't undergone code review, which appears to be the reason for the separate branch approach. He acknowledges that the original branch is actually in good condition, with the main issue being that previously completed changes from the develop branch weren't properly pushed to it. This suggests a workflow problem rather than fundamental issues with the codebase itself.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T08:14:05.346000+00:00] barnabasbusa: any reason you guys don’t push to epbs devnet 0 branch
[2026-03-05T08:14:09.797000+00:00] barnabasbusa: why bother having another?
[2026-03-05T08:14:21.091000+00:00] barnabasbusa: it’s not like the original branch is working now lol
[2026-03-05T09:53:28.847000+00:00] potuz: Syncing wasn't reviewed by anyone
[2026-03-05T09:54:08.567000+00:00] potuz: The original branch is solid, if only we remembered to push changes we already had done to develop into it 🙂
```

</details>
