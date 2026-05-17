# Builder Structure Versioning and ABS Discussion

**Channel:** epbs | **Date:** 2025-12-29

## Summary

The discussion centers around a PR that makes builders non-validators, with potuz raising concerns about the lack of versioning in the current builder structure design. He argues that using execution addresses instead of full withdrawal credentials removes the versioned type benefits that validators currently have, and warns this could become problematic in future forks when builder types need modifications (similar to how validator types evolved to 0x01 and 0x02 versions).

Potuz also introduces the concept of ABS (attester-builder separation), where builders would be randomly chosen by the protocol rather than by proposers. He suggests this could lead to a significant portion of stake moving to the builder set (proportional to execution layer rewards), making versioned builder types and potentially moving balances outside the current slice structure more important for scalability and future flexibility.

jtraglia proposes adding a simple "version" field to the Builder structure as a potential solution and commits to investigating this approach. The PR is currently blocking releases, with potuz reviewing it as a priority item for resolution.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-29T12:01:40.857000+00:00] potuz: <@592004585506340885> An off-comment on the PR to make builders non-validators. I know I suggested to use the execution address instead of the full withdrawal credentials, but one nice feature of validators is that we get a *versioned* type without the need to modify it on each fork. We are already arguably on the third type for this. I feel like this will bite us back if we were to ever need to modify the builder type to have a different feature for them (like with did with 0x01 or 0x02 validators). Incidentally, I will eventually propose, or someone else will, a form of APS, which should probably be called ABS (attester-builder separation) in this context in which simply builders are chosen randomly from the set of builders. If we ever move to such a scheme (or any scheme I can think of in which builders are protocol chosen instead of proposer-chosen) then it is natural to expect a large stake proportion to move to this set (naively it would be the proportion of EL rewards with respect to the total reward). This leads me to believe that having some "versioned" type for builders could be useful in the future and this also brings me to the second part of the comment: if it ever happens that we do end up with a large set of builders by stake, it may make sense to move the balances also outside of this slice.
[2025-12-29T14:34:11.550000+00:00] jtraglia: Could we just add a “version” field to the Builder structure?
[2025-12-29T14:34:35.308000+00:00] potuz: we could
[2025-12-29T14:37:15.186000+00:00] jtraglia: I will look into this when I get a chance (it will be tomorrow at the earliest).
[2025-12-29T14:58:55.333000+00:00] potuz: I'm going through the PR right now cause Terence told me it's a blocker for us until we release
[2025-12-29T15:13:13.855000+00:00] jtraglia: Yes, it is blocking things. Thanks.
```

</details>
