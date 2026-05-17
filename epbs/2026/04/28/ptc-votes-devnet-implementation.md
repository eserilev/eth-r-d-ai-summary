# PTC votes counting implementation

**Channel:** epbs | **Date:** 2026-04-28

## Summary

Potuz opened a discussion about PR #5186 related to PTC (Payload Timeliness Committee) votes counting implementation, seeking input from team members. The implementation approach remains undecided between using an existing PR by Cayman or adding a counter for total PTC votes, with Potuz noting they plan to implement the latter internally in Prysm to avoid increasing memory usage by 64 bytes per node.

When jtraglia requested fixing failing tests and adding new test coverage, Potuz indicated this work is blocked as the implementation depends on Cayman's PR or a similar solution for counting attestations. The discussion leaves the PR in a pending state awaiting resolution of the underlying attestation counting dependency.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-28T15:53:18.340000+00:00] potuz: <@592004585506340885> <@586161934425128960> take a look at this one? https://github.com/ethereum/consensus-specs/pull/5186
[2026-04-28T15:54:18.505000+00:00] potuz: I don't know if you will decide to go with <@534934855113506836>'s PR as mentioned in the description or just add a counter for total PTC votes (which is the way I'll implement internally in Prysm to not increase 64 bytes per node)
[2026-04-28T17:33:57.931000+00:00] jtraglia: Interesting. Could you fix the failing tests & add a test or two for this?
[2026-04-28T17:43:51.666000+00:00] potuz: Nah this depends on Cayman's PR or some similar way to count attestations
```

</details>
