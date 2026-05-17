# Fork choice abstraction layer PR review

---

## 2026-05-13 (epbs)

The discussion centers around PR #5249, which extends the fork choice abstraction layer to facilitate easier upgrades from Phase0 to Gloas. According to m.kalinin, the PR doesn't introduce semantic changes but rather improves readability and testability of the fork choice specification, addressing issues encountered when working with the Gloas fork choice spec.

Potuz requested to hold the PR for additional reviews, expressing willingness to accept pre-Gloas logic changes but wanting to carefully verify that post-Gloas behavior remains unchanged or that any changes are well understood. Potuz also referenced ongoing issues with spectests being skipped due to backporting from a previous PR (#4807), which they hope to resolve soon.

The discussion highlights the careful review process needed for fork choice changes and the interconnected nature of specification updates, particularly around the transition to Gloas.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-13T18:24:32.193000+00:00] m.kalinin: It would be great if someone would take a look at this PR: https://github.com/ethereum/consensus-specs/pull/5249
It does an important thing and extends fork choice abstraction layer so it can be modified in Gloas and make the upgradability from Phase0 to Gloas easier than it is today.

Impls doesn't need to change anything as ultimately this PR doesn't introduce any semantics change and rather addresses readability and testability issues of the spec which I personally had when started to read and work with the fork choice spec in Gloas
[2026-05-13T20:46:29.874000+00:00] potuz: can we hold that one until we get a few views? I don't mind if the logic is slightly changed pre-Gloas, but I want to really check that nothing changes post-Gloas, or that at least I understand the changes
[2026-05-13T20:51:13.074000+00:00] potuz: For reference, the backporting caused in <https://github.com/ethereum/consensus-specs/pull/4807> still has us skipping some spectests. I hope I can unskip them soon.
```

</details>

---

## 2026-05-14 (epbs)

The discussion centers on fork choice (FC) specification changes and their impact on existing implementations. m.kalinin clarifies that the proposed changes are purely specification-level modifications that shouldn't affect most implementations, since fork choice implementations typically differ significantly from the spec. The main exception would be implementations that directly use functions like `is_supporting_vote` as defined in the spec, which would need to be replaced with new functions as outlined in the PR.

potuz explains that their implementation predates the specification and doesn't use the spec's function abstractions, having only minimally adapted to Francesco's changes for the new proposer boost (PB-based) reorg system. They express uncertainty about which backports may have caused issues with long justified checkpoint reorgs under withholding attacks, noting plans to investigate this while working on equivocation checks.

m.kalinin confirms there are no backports in the current PR, with plans to work on backporting separately after the current changes are merged. Given that potuz's implementation doesn't rely heavily on the spec's abstractions, the current PR should not cause any issues for their codebase.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-14T04:43:51.618000+00:00] m.kalinin: sure! These changes shouldn't affect implementations as FC impls are quite different comparing to the spec. Why would you want to change anything in your code if that's purely a spec change? It maybe that your code have `is_supporting_vote` as it is in the spec then you might want to replace it with new function as the PR does. But, in general why any FC *spec* backporting would affect impls?
[2026-05-14T11:58:27.096000+00:00] potuz: I don't have any of the functions as in the spec since my implementation predates the spec :), Francesco changed a bunch of the logic I had in the original spec and I minimally adapted to the new PB-based reorg system. I am uncertain now as to which backports there caused trouble with some of our long justified checkpoints reorgs under withholding. I will tackle them soon now that I am merging the equivocation checks.
[2026-05-14T17:09:26.834000+00:00] m.kalinin: I see. There are no backports in the PR yet, I am planning to work on it separately after current PR is merged. As you don't use abstraction of the the spec much in the impl then this PR should be completely fine for you
```

</details>

