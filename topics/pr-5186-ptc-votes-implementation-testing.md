# PR 5186 PTC votes implementation and testing

---

## 2026-04-28 (epbs)

potuz shared PR 5186 for PTC (Payload Timeliness Committee) votes implementation and testing, requesting review from team members. The implementation approach is still being debated between using a referenced PR from another contributor versus adding a counter for total PTC votes. potuz mentioned they plan to implement internally in Prysm using a counter approach to avoid increasing memory usage by 64 bytes per node.

When jtraglia requested fixing failing tests and adding new test coverage, potuz indicated the work is blocked as it depends on Cayman's PR or a similar solution for counting attestations. The PR remains in development with unresolved dependencies and failing tests that cannot be addressed until the underlying attestation counting mechanism is implemented.

**Participants:** jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-28T15:53:18.340000+00:00] potuz: <@592004585506340885> <@586161934425128960> take a look at this one? https://github.com/ethereum/consensus-specs/pull/5186
[2026-04-28T15:54:18.505000+00:00] potuz: I don't know if you will decide to go with <@534934855113506836>'s PR as mentioned in the description or just add a counter for total PTC votes (which is the way I'll implement internally in Prysm to not increase 64 bytes per node)
[2026-04-28T17:33:57.931000+00:00] jtraglia: Interesting. Could you fix the failing tests & add a test or two for this?
[2026-04-28T17:43:51.666000+00:00] potuz: Nah this depends on Cayman's PR or some similar way to count attestations
```

</details>

