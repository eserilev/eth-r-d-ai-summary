# PR reviews for consensus specs

**Channel:** epbs | **Date:** 2026-04-16

## Summary

This discussion centers around reviewing two consensus specs PRs (#5117 and #4840) related to BAL (block auction logic) and slot precompile functionality. The main technical issue raised by terencechain is whether the execution payload should include both BAL and slot number precompiles, noting that the engine API spec includes `slotNumber: QUANTITY, 64 Bits` but questioning if PR #5117 diverges by only including BAL without the slot number.

After discussion, there's consensus among the developers (qu0b, barnabasbusa, and others) that both BAL and slot precompiles should be included, as the slot functionality has been part of the BAL devnet for a long time and shouldn't be removed now. jtraglia acknowledges missing this requirement and agrees to update the PR, though suggests waiting until #5117 is merged to make the ExecutionPayload changes easier.

The conversation also briefly touches on PR #5088 regarding impossible condition checks, with jtraglia preferring to remove them unless needed in the future. The discussion concludes with urgency around getting releases and spec tests completed soon, as client implementation and review will be the bottleneck going forward.

## Participants

- barnabasbusa
- jtraglia
- qu0b
- terencechain
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-16T16:22:17.280000+00:00] jtraglia: <@&1417820113981145228> could I get some reviews on these two PRs:
* https://github.com/ethereum/consensus-specs/pull/5117
* https://github.com/ethereum/consensus-specs/pull/4840
[2026-04-16T16:45:53.394000+00:00] terencechain: is slot pre compile in? as i review: https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md
there is `slotNumber: QUANTITY, 64 Bits`
so do we want to create another situation that diverge from engine api that only consits of BAL but not slot number?
[2026-04-16T16:52:39.348000+00:00] jtraglia: I don't fully understand the question. Isn't this required for interop?
[2026-04-16T16:53:55.658000+00:00] terencechain: yes, you did it in 4840, but dont we want it in 5117 too?
[2026-04-16T16:54:21.723000+00:00] terencechain: the payload needs to contain the slot number right?
[2026-04-16T16:55:49.214000+00:00] jtraglia: Hmm you're right. I missed that. Good catch. Will update.
[2026-04-16T16:57:25.355000+00:00] jtraglia: I'm not sure if there was an ExecutionPayload change in the EIP when I made this PR back in January
[2026-04-16T16:58:37.876000+00:00] jtraglia: It would be easier to make this change after 5117 is merged. I'm just going to wait.
[2026-04-16T17:01:55.890000+00:00] terencechain: the question is more, do we accept both BAL and slot pre compile or BAL only? 
i know for BAL yes but i didnt get a sense for slot pre compile during the call (probably i missed it)
i dont have a strong preference either
[2026-04-16T17:21:06.722000+00:00] jtraglia: cc <@412614104222531604>
[2026-04-16T17:23:06.445000+00:00] qu0b: i'd prefer to have both
[2026-04-16T17:55:37.008000+00:00] barnabasbusa: we should do both
[2026-04-16T17:55:54.030000+00:00] barnabasbusa: as it’s been part of bals devnet for a long time now
[2026-04-16T17:56:06.268000+00:00] barnabasbusa: we shouldn’t rip it out now
[2026-04-16T18:01:12.781000+00:00] jtraglia: Also devs, thoughts on this PR?
https://github.com/ethereum/consensus-specs/pull/5088
[2026-04-16T18:01:21.815000+00:00] jtraglia: Sorry, there's so much to review 😢
[2026-04-16T18:02:04.293000+00:00] jtraglia: My preference is that we shouldn't have impossible condition checks & if in the future we need this, we can always add it back.
[2026-04-16T18:02:12.589000+00:00] jtraglia: cc <@654267572107083777>
[2026-04-16T18:04:04.212000+00:00] terencechain: ok in that case <@755590043632140352> might want to update his PR or it will be a different PR. 
the sooner we can have a release / spec tests the better because bottleneck will be on the client implementation and review
[2026-04-16T18:05:51.902000+00:00] tersec: yeah I agree
```

</details>
